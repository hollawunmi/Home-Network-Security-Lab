# Home Network Security Lab

**Author:** Segun Olawunmi — Cybersecurity Analyst

---

## Overview

This project documents the design and implementation of an enterprise-grade segmented home network built for both practical security and portfolio demonstration purposes. The lab demonstrates real-world network segmentation, firewall policy enforcement, wireless security, and VPN configuration using consumer/prosumer hardware.

---

## Hardware

| Device | Model | IP Address | Role |
|--------|-------|------------|------|
| ISP Modem | Sagemcom Hub 4000 | 192.168.2.1 | ISP gateway (CG-NAT) |
| Router | TP-Link ER605 | 192.168.10.1 / 192.168.0.1 | Core router, VLAN gateway, VPN server |
| Switch | TP-Link TL-SG108E | 192.168.0.101 | Managed 8-port switch |
| Access Point | TP-Link EAP245 V3 | 192.168.0.102 | Dual-band WiFi AP |

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

### Network Topology

```
Virgin Plus → Hub 4000 (192.168.2.1)
                  ↓ LAN (Cat6)
             ER605 WAN port
                  ↓ LAN port 1 (Cat6 trunk)
             TL-SG108E Switch
          ↓           ↓          ↓         ↓
       Port 3      Port 4    Port 5    Port 7
      Laptop/     Android   Home TV   PoE Injector
      Trusted       TV               → EAP245 AP
      (VLAN10)   (VLAN20)  (VLAN20)
                              ↓
                           Port 6
                            PS5
                          (VLAN20)
```

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

WireGuard VPN server configured on the ER605 for encrypted remote access. CG-NAT from Virgin Plus prevents direct external connections — Tailscale used as a relay for remote access.

---

## Verification

VLAN isolation was verified using ping tests across all zone boundaries:

| Test | From | To | Result |
|------|------|----|--------|
| 1 | Trusted | Internet (8.8.8.8) | ✅ Pass |
| 2 | IoT | Trusted | ✅ Blocked |
| 3 | Guest | IoT | ✅ Blocked |

---

## Repository Structure

```
home-network-security-lab/
├── README.md
├── diagrams/
│   └── network-topology.md
├── config/
│   ├── vlan-table.md
│   ├── firewall-rules.md
│   ├── switch-port-config.md
│   └── vpn-setup.md
├── threat-model/
│   └── threat-model.md
└── verification/
    └── test-results.md
```

---

## Skills Demonstrated

- Network segmentation with 802.1Q VLANs
- Firewall ACL policy design and implementation
- Managed switch configuration (trunk/access ports, PVID)
- Wireless security zones and captive portal
- WireGuard VPN server deployment
- CG-NAT traversal with Tailscale
- Network documentation and threat modelling
