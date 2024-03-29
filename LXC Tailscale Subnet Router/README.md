
<h2>First we need to Create and Configure a LXC container on each Proxmox host in each Site</h2>
<br />
<p>1. First create a LXC container of your choice on each Proxmox host in each Remote Site. I chose Ubuntu server 22.04 so my documentation will be specific to this distro but the same overall concepts will work for other distros out there. <br /><br />
<img src="https://github.com/shaunvaidyan/Proxmox-Headscale-Tailscale-TechnitiumDNS/blob/main/assets/LXC Container Templates.png" style="border-radius:50%;align-items:center;justify-content:center;overflow:hidden;padding-top:10px"/>
<sub style="font-size:16px" align="center"><b>In the Proxmox GUI click on your local storage pool, then go into CT Templates, and click on the Templates button to see a list of turnkey LXC contaner templates you can easily download. Of course you can configure your own custom LXC image and upload it too or download from another URL. </b></sub></p><br />
<p>2. Spin up a LXC container using the template you downloaded by right-clicking on your node and pressing Create CT. <br /><br />
<img src="https://github.com/shaunvaidyan/Proxmox-Headscale-Tailscale-TechnitiumDNS/blob/main/assets/LXC config.png" style="border-radius:50%;align-items:center;justify-content:center;overflow:hidden;padding-top:10px"/>
<sub style="font-size:16px" align="center"><b>These are the settings I used with the unprivileged container and nesting options checked. Specify your own password and optionally upload an ssh key file. Keep note of your CT ID as you will need it later. For Disk Size I found that my Tailscale subnet router only uses 1.6 GB of space but space isn't a premium for me so I allocated 12GB for the disk size. 1 CPU and 2 GB of RAM is more than enough for the container as well. For the network options I would recommend a static IP and record it. Besides that all the default options are fine</b></sub></p><br />

<p>3. Make sure your container is Shut Down and go to your Proxmox HOST shell. Then we need to navigate to the config of your newly created LXC container and make some changes. Recall the CT ID of the container you just created. In my case it is 113 so the config I need to edit is /etc/pve/lxc/113.conf. These commands are required to ensure that Tailscale runs properly in a LXC unprivileged environment with access to the /dev/tun device ro run them in the <b>PROXMOX HOST</b> shell:
   
```
echo 'lxc.cgroup2.devices.allow: c 10:200 rwm' | tee -a /etc/pve/lxc/113.conf
echo 'lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file' | tee -a /etc/pve/lxc/113.conf
```
With the previous step complete, go ahead and start/restart your LXC. We need to make some changes to sysctl.conf in the LXC to enable ipv4 and ipv6 forwarding to ensure the proper routing of traffic to the local subnet routes so run these commands in the <b>LXC</b> shell:

```
echo 'net.ipv4.ip_forward = 1' | tee -a /etc/sysctl.conf
echo 'net.ipv6.conf.all.forwarding = 1' | tee -a /etc/sysctl.conf
sysctl -p /etc/sysctl.conf
```
</p>
<br />
<br />

<h2>Now Install Tailscale on each LXC subnet router container on each site</h2>
Tailscale provides an all in one bash script installer:

```
curl -sSL https://tailscale.com/install.sh | sh
```

<h2>We need to make some Linux optimizations for subnet routers and exit nodes</h2>
<p>1. Now that tailscale is finished installing, install ethtool</p>

```
apt install ethtool
```
<p>2. Run these commands to enable UDP throughput improvements via transport layer offloads and ensuring things work on hosts with 10GBe interfaces that use MTU values of 9000. These settings will persist on boots and reboots:</p>

```
printf '#!/bin/sh\n\nethtool -K %s rx-udp-gro-forwarding on rx-gro-list off \n' "$(ip route show 0/0 | cut -f5 -d" ")" | tee /etc/networkd-dispatcher/routable.d/50-tailscale
echo 'iptables -t mangle -A FORWARD -i tailscale0 -o eth0 -p tcp -m tcp \
--tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu' >> /etc/networkd-dispatcher/routable.d/50-tailscale
chmod 755 /etc/networkd-dispatcher/routable.d/50-tailscale
```
   
 
<h2>Connect Tailscale Subnet Router to Headscale Coordination Server</h2>
<p>This command configures our Tailscale client to connect to our Self-hosted Headscale coordination server with the --login-server flag. We also need to advertise all the Local Routes/Subnets/VLANs you want to share from each site 
</p>
<h3 align="center">Example Site 1</h3>

```
systemctl enable --now tailscaled
tailscale up --login-server=https://headscale.example.com --advertise-routes=192.168.0.0/23 --snat-subnet-routes=false --accept-dns=true --accept-routes
```



References:
1. https://tailscale.com/kb/1130/lxc-unprivileged#instructions
2. https://tailscale.com/kb/1214/site-to-site
3. https://tailscale.com/kb/1023/troubleshooting#my-firewall-blocks-everything-by-default-which-ports-do-i-need-to-open
