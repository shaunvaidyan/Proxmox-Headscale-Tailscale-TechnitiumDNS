```nano /usr/local/sbin/myrouting```

```
#!/bin/bash
ip rule add to 192.168.1.0/24 priority 2500 lookup main
ip rule add to 192.168.10.0/24 priority 2500 lookup main
ip rule add to 192.168.20.0/24 priority 2500 lookup main
ip rule add to 192.168.30.0/24 priority 2500 lookup main
```

```nano /etc/systemd/system/myrouting.service```

```chmod +x myrouting```

```
[Unit]
Description=Routing to Prioritize Local routes if available over Tailscale Routing
after=tailscaled.service

[Service]
ExecStart=/usr/local/sbin/myrouting

[Install]
WantedBy=multi-user.target
```
