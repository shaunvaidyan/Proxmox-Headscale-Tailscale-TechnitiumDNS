<p>
	<h1 align="center"><b>Self-Hosting a Site to Site VPN & DNS using Tailscale, Headscale, Proxmox, and Technitium</b></h1><br />
	<table align="center">
		<tr>
    			<td align="center" style="word-wrap: break-word; width: 100.0; height: 100.0">
        			<a href=https://www.proxmox.com/en/>
            			<img src="https://github.com/shaunvaidyan/Proxmox-Headscale-Tailscale-TechnitiumDNS/blob/main/assets/proxmox.png" width="100" height="100" style="border-radius:50%;align-items:center;justify-content:center;overflow:hidden;padding-top:10px" alt=Proxmox Hypervisor/><br />
            			<sub style="font-size:12px"><b>Proxmox</b></sub>
				</a>
    			</td>
			<td align="center" style="word-wrap: break-word; width: 100.0; height: 100.0">
        			<a href=https://headscale.net/>
            			<img src="https://github.com/shaunvaidyan/Proxmox-Headscale-Tailscale-TechnitiumDNS/blob/main/assets/headscale.png" width="100" height="100" style="border-radius:50%;align-items:center;justify-content:center;overflow:hidden;padding-top:10px" alt=Headscale/><br />
            			<sub style="font-size:12px"><b>Headscale</b></sub>
				</a>
    			</td>
			<td align="center" style="word-wrap: break-word; width: 100.0; height: 100.0">
        			<a href=https://headscale.net/>
            			<img src="https://github.com/shaunvaidyan/Proxmox-Headscale-Tailscale-TechnitiumDNS/blob/main/assets/tailscale.png" width="100" height="100" style="border-radius:50%;align-items:center;justify-content:center;overflow:hidden;padding-top:10px" alt=Tailscale/><br />
            			<sub style="font-size:12px"><b>Tailscale</b></sub>
				</a>
    			</td>
			<td align="center" style="word-wrap: break-word; width: 100.0; height: 100.0">
        			<a href=https://tailscale.com>
            			<img src="https://github.com/shaunvaidyan/Proxmox-Headscale-Tailscale-TechnitiumDNS/blob/main/assets/technitium.png" width="100" height="100" style="border-radius:50%;align-items:center;justify-content:center;overflow:hidden;padding-top:10px" alt=Technitium DNS Server/><br />
            			<sub style="font-size:12px"><b>Technitium</b></sub>
				</a>
    			</td>
		</tr>	
	</table>
	<br />
	<br />
	I wanted to document how I connected two remote sites together using a self-hosted Headscale instance &amp; on Ubuntu VMs on Proxmox hypervisors
	<br />
	<br />
</p>

References:<br />
1. https://tailscale.com/kb/1214/site-to-site
2. https://headscale.net/running-headscale-linux/
