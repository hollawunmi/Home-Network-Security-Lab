# Home Network Security Lab

**Author:** Segun Olawunmi — Cybersecurity Analyst 

---

## Overview

This project documents the design and implementation of an enterprise-grade segmented home network built for both practical security and portfolio demonstration purposes. The lab demonstrates real-world network segmentation, firewall policy enforcement, wireless security, and VPN configuration using consumer/prosumer hardware.

---

## Hardware

| Device | Model | IP Address | Role | Cost (CAD) |
|--------|-------|------------|------|------------|
| ISP Modem | Sagemcom Hub 4000 | 192.168.2.1 | ISP gateway (CG-NAT) | ISP provided |
| Router | TP-Link ER605 | 192.168.10.1 / 192.168.0.1 | Core router, VLAN gateway, VPN server | $84.99 |
| Switch | TP-Link TL-SG108E | 192.168.0.101 | Managed 8-port switch | $34.99 |
| Access Point | TP-Link EAP245 V3 | 192.168.0.102 | Dual-band WiFi AP | $112.99 |
| Cabling | UGREEN Cat6 x6 | — | Physical connections | $51.24 |

**Total hardware cost: $284 CAD (~$205 USD)**

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
| IoT_Isolated | 2.4GHz + 5GHz | VLAN 20 | WPA2-PSK |
| Guest_WiFi | 5GHz only | VLAN 30 | Captive Portal |

---

## VPN

WireGuard VPN server configured on the ER605 for encrypted remote access. CG-NAT from Virgin Plus prevents direct external connections Tailscale used as a relay for remote access.

---

## Verification

VLAN isolation was verified using ping tests across all zone boundaries:

| Test | From | To | Result |
|------|------|----|--------|
| 1 | Trusted | Internet (8.8.8.8) | ✅ Pass |
| 2 | IoT | Trusted | ✅ Blocked |
| 3 | Guest | IoT | ✅ Blocked |

---

## Screenshot Documentation

All configuration steps are documented with screenshots in the `screenshots/` folder. Key screenshots:

| Screenshot | Description |
|------------|-------------|
| 02 | ER605 WAN connected: IP 192.168.2.43 |
| 08 | All 4 VLANs created on ER605 |
| 19 | All 4 firewall isolation rules complete |
| 23b | Hand-drawn switch port plan (planning phase) |
| 27 | Switch PVID settings complete |
| 39 | EAP245 VLAN assignments: SSIDs mapped to VLANs 10/20/30 |
| 41 | Switch final VLAN config: all 4 VLANs with Port 7 trunk |
| 46 | WireGuard peer handshake active: 97MB TX confirmed |
| 48 | Hub 4000 port forwarding: UDP 51820 → ER605 |

---

## Skills Demonstrated

- Network segmentation with 802.1Q VLANs
- Firewall ACL policy design and implementation
- Managed switch configuration (trunk/access ports, PVID)
- Wireless security zones and captive portal
- WireGuard VPN server deployment
- CG-NAT traversal with Tailscale
- Network documentation and threat modelling
