# Home Network Security Lab
**Author:** Segun Olawunmi — Cybersecurity Analyst 
---
## Overview
This project documents the design and implementation of an enterprise-grade segmented home network built for both practical security and portfolio demonstration purposes. The lab demonstrates real-world network segmentation, firewall policy enforcement, wireless security, DNS filtering, remote access, and SIEM monitoring using consumer/prosumer hardware.

---
## Hardware
| Device | Model | IP Address | Role | Cost (CAD) |
|--------|-------|------------|------|------------|
| ISP Modem | Sagemcom Hub 4000 | 192.168.2.1 | ISP gateway (CG-NAT) | ISP provided |
| Router | TP-Link ER605 | 192.168.10.1 / 192.168.0.1 | Core router, VLAN gateway, VPN server | $84.99 |
| Switch | TP-Link TL-SG108E | 192.168.0.101 | Managed 8-port switch | $34.99 |
| Access Point | TP-Link EAP245 V3 | 192.168.0.103 | Dual-band WiFi AP | $112.99 |
| Security Server | Raspberry Pi 5 (8GB) | 192.168.0.102 | Pi-hole DNS, Tailscale, Wazuh agent | $149.99 |
| Cabling | UGREEN Cat6 x6 | — | Physical connections | $51.24 |

**Total hardware cost: $434 CAD (~$315 USD)**  
**ISP:** Virgin Plus (Bell Canada) — 580 Mbps down, CG-NAT confirmed

---
## Network Architecture

### VLAN Design
| VLAN | Name | Subnet | Purpose |
|------|------|--------|---------|
| 1 | Management | 192.168.0.0/24 | Switch and AP management |
| 10 | Trusted | 192.168.10.0/24 | Family devices, laptops, phones |
| 20 | IoT_Isolated | 192.168.20.0/24 | Android TV, Smart TV, PS5, IoT devices |
| 30 | Guest_WiFi | 192.168.30.0/24 | Visitors, untrusted devices |

### Network Topology Diagram
![Network Topology](diagrams/network-topology.png)

---
## Security Model
**Principle:** Least privilege network access. Each zone gets only what it needs.

| Zone | Internet | Trusted Access | IoT Access | Guest Access |
|------|----------|----------------|------------|--------------|
| Trusted (VLAN10) | ✅ | ✅ | ❌ | ❌ |
| IoT (VLAN20) | ✅ | ❌ | ✅ | ❌ |
| Guest (VLAN30) | ✅ | ❌ | ❌ | ❌ |

Firewall rules enforce zero lateral movement between zones. IoT and Guest devices get internet access only.

---
## WiFi SSIDs
| SSID | Band | VLAN | Security |
|------|------|------|----------|
| HomeNetwork | 2.4GHz + 5GHz | VLAN 10 | WPA2-PSK |
| IoT_Isolated | 5GHz only | VLAN 20 | WPA2-PSK |
| Guest_WiFi | 5GHz only | VLAN 30 | Captive Portal |

---
## Firewall Policy
9 access control rules enforcing strict VLAN isolation:

| Rule | Direction | Action | Purpose |
|------|-----------|--------|---------|
| Block_IoT_to_Trusted | LAN→LAN | Block | Prevent IoT reaching trusted devices |
| Block_Guest_to_Trusted | LAN→LAN | Block | Prevent guests reaching trusted devices |
| Block_Guest_to_IoT | LAN→LAN | Block | Prevent lateral movement |
| Block_IoT_to_Guest | LAN→LAN | Block | Prevent lateral movement |
| Allow_Trusted_to_Management | LAN→LAN | Allow | Management access from VLAN10 |
| Block_Trusted_to_IoT | LAN→LAN | Block | Prevent trusted initiating IoT connections |
| Block_Trusted_to_Guest | LAN→LAN | Block | Prevent trusted initiating guest connections |
| Block_IoT_DNS_Bypass | LAN→LAN | Block | Force IoT through Pi-hole DNS |
| Block_Guest_DNS_Bypass | LAN→LAN | Block | Force Guest through Pi-hole DNS |

---
## Security Stack

### Pi-hole DNS Filtering
- Network-wide ad and malware blocking via DNS
- Upstream DNS: Cloudflare (1.1.1.1) with DNSSEC
- Blocklist: StevenBlack's Unified Hosts List
- All VLANs configured to use Pi-hole as primary DNS
- DNS bypass prevention enforced via firewall rules
- See: [pi-hole-setup.md](pi-hole-setup.md)

### Tailscale Remote Access
- Bypasses Virgin Plus CG-NAT using NAT traversal
- Subnet router advertising all 4 VLANs remotely
- Full network management accessible from anywhere
- Authentication via GitHub SSO
- See: [tailscale-setup.md](tailscale-setup.md)

### Wazuh SIEM
- Wazuh Manager + Dashboard running on laptop via Docker
- Wazuh Agent deployed on Raspberry Pi
- Active monitoring: SSH brute force, file integrity, privilege escalation
- MITRE ATT&CK framework mapping
- See: [wazuh-setup.md](wazuh-setup.md)

---
## Verification Testing

| Test | From | To | Result |
|------|------|----|--------|
| 1 | Trusted | Internet (8.8.8.8) | ✅ Pass |
| 2 | IoT device | Trusted | ✅ Blocked |
| 3 | Trusted | IoT device | ✅ Blocked |
| 4 | Guest | IoT | ✅ Blocked |
| 5 | DNS via Pi-hole | All VLANs | ✅ Confirmed |
| 6 | SSH brute force | Wazuh detection | ✅ Rule 2502, Level 10 |
| 7 | Tailscale remote SSH | External → Pi | ✅ Working |

---
## Screenshot Documentation
All configuration steps documented with screenshots in the `screenshots/` folder:

| Screenshot | Description |
|------------|-------------|
| 02 | ER605 WAN connected: IP 192.168.2.43 |
| 08 | All 4 VLANs created on ER605 |
| 19 | All 9 firewall rules complete |
| 23b | Hand-drawn switch port plan (planning phase) |
| 27 | Switch PVID settings complete |
| 39 | EAP245 VLAN assignments: SSIDs mapped to VLANs 10/20/30 |
| 41 | Switch final VLAN config: all 4 VLANs with Port 7 trunk |
| 46 | WireGuard peer handshake active |
| 48 | Hub 4000 port forwarding: UDP 51820 → ER605 |

---
## Skills Demonstrated
- Network segmentation with 802.1Q VLANs
- Firewall ACL policy design and implementation (9 rules)
- Managed switch configuration (trunk/access ports, PVID)
- Wireless security zones and captive portal
- DNS security and filtering (Pi-hole)
- Zero Trust remote access (Tailscale, CG-NAT traversal)
- SIEM deployment and threat detection (Wazuh)
- MITRE ATT&CK framework mapping
- Network documentation and threat modelling