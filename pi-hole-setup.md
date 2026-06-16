\# Pi-hole DNS Filtering Setup



\## Overview

Pi-hole provides network-wide DNS filtering, blocking ads, tracking domains, and malware at the DNS level for all devices across all VLANs.



\## Hardware

\- Raspberry Pi 5 (8GB RAM)

\- Raspberry Pi OS Lite 64-bit (headless)

\- Connected to Switch Port 8 (VLAN1)

\- Static IP: 192.168.0.102 (DHCP reservation on ER605)



\## Installation



\### 1. Flash OS

\- Tool: Raspberry Pi Imager v2.0.7

\- OS: Raspberry Pi OS Lite (64-bit)

\- SSH enabled during imaging



\### 2. Install Pi-hole

```bash

curl -sSL https://install.pi-hole.net | bash

```



\### Configuration

| Setting | Value |

|---------|-------|

| Interface | eth0 |

| Upstream DNS | Cloudflare (1.1.1.1) |

| Blocklist | StevenBlack's Unified Hosts List |

| Query Logging | Enabled |

| Privacy Level | Show everything |

| Admin dashboard | http://192.168.0.102/admin |



\## VLAN DNS Configuration

Pi-hole set as primary DNS on all VLANs via ER605 DHCP settings:



| VLAN | Primary DNS | Secondary DNS |

|------|-------------|---------------|

| VLAN10 Trusted | 192.168.0.102 | 1.1.1.1 |

| VLAN20 IoT\_Isolated | 192.168.0.102 | 1.1.1.1 |

| VLAN30 Guest\_WiFi | 192.168.0.102 | 1.1.1.1 |



\## DNS Bypass Prevention

Two firewall rules added to ER605 to prevent devices bypassing Pi-hole:

\- `Block\_IoT\_DNS\_Bypass` — blocks IoT devices using external DNS

\- `Block\_Guest\_DNS\_Bypass` — blocks Guest devices using external DNS



\## Static IP Reservation

MAC address `2C-CF-67-BF-2D-73` reserved to 192.168.0.102 on ER605.

