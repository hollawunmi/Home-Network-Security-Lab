\# Tailscale Remote Access Setup



\## Overview

Tailscale provides secure remote access to the home network, bypassing Virgin Plus CG-NAT limitations that prevent traditional VPN solutions like WireGuard from working externally.



\## Why Tailscale

\- Virgin Plus uses CG-NAT (Hub WAN IP: 10.133.4.138) — port forwarding alone insufficient

\- Tailscale uses NAT traversal to establish direct encrypted connections

\- Zero Trust model — every device authenticated before access granted

\- Free tier supports up to 100 devices



\## Installation



\### On Raspberry Pi (exit node / subnet router)

```bash

curl -fsSL https://tailscale.com/install.sh | sh

sudo tailscale up --advertise-routes=192.168.0.0/24,192.168.10.0/24,192.168.20.0/24,192.168.30.0/24

```



\### Enable IP Forwarding

```bash

echo 'net.ipv4.ip\_forward = 1' | sudo tee -a /etc/sysctl.conf

sudo sysctl -p

sudo ethtool -K eth0 rx-udp-gro-forwarding on rx-gro-list off

```



\## Device Inventory

| Device | Tailscale IP | Role |

|--------|-------------|------|

| pihole | 100.122.220.89 | Subnet router |

| joshua | 100.84.249.29 | Laptop client |



\## Subnet Routes Approved

| Subnet | Network |

|--------|---------|

| 192.168.0.0/24 | Management (VLAN1) |

| 192.168.10.0/24 | Trusted (VLAN10) |

| 192.168.20.0/24 | IoT\_Isolated (VLAN20) |

| 192.168.30.0/24 | Guest\_WiFi (VLAN30) |



\## Remote Access Capability

From anywhere in the world via Tailscale:

\- ER605 router: 192.168.10.1

\- TL-SG108E switch: 192.168.0.101

\- Pi-hole dashboard: 192.168.0.102/admin

\- SSH to Pi: ssh houstonj@100.122.220.89



\## Authentication

\- Tailscale account linked via GitHub SSO

\- Tailnet name: hollawunmi.github

