# Threat Model

**Project:** Home Network Security Lab  
**Author:** Segun Olawunmi  
**Date:** May 2026  

---

## Assets

| Asset | Value | Location |
|-------|-------|----------|
| Personal laptops and phones | High | VLAN10 (Trusted) |
| Personal data and credentials | High | VLAN10 devices |
| Network infrastructure (router, switch, AP) | High | VLAN1 (Management) |
| Smart home / IoT devices | Medium | VLAN20 (IoT) |
| Guest devices | Low | VLAN30 (Guest) |
| Internet bandwidth | Medium | All VLANs |

---

## Threat Actors

| Actor | Likelihood | Capability |
|-------|------------|------------|
| Compromised IoT device | Medium | Low–Medium |
| Malicious guest user | Low | Low–Medium |
| External attacker (internet) | Medium | Medium–High |
| Neighbour on shared WiFi | Low | Low |

---

## Threats and Mitigations

### 1. Compromised IoT Device — Lateral Movement
**Threat:** A vulnerable smart TV or IoT device is exploited and used to attack trusted devices (laptops, phones).  
**Likelihood:** Medium — IoT devices are frequently unpatched and targeted.  
**Mitigation:** VLAN20 (IoT_Isolated) has no route to VLAN10 (Trusted). Firewall rule Block_IoT_to_Trusted enforces this at the router level. Even a fully compromised IoT device cannot reach trusted devices.  
**Residual Risk:** Low

---

### 2. Malicious Guest User — Internal Reconnaissance
**Threat:** A guest connects to Guest_WiFi and attempts to scan or attack internal devices.  
**Likelihood:** Low  
**Mitigation:** VLAN30 (Guest) is isolated from both Trusted and IoT zones. Guest devices get internet-only access. Captive portal adds friction for casual attackers. 5GHz only limits physical range of the SSID.  
**Residual Risk:** Low

---

### 3. External Attacker — WAN Exploitation
**Threat:** An attacker from the internet attempts to reach internal devices via the router's WAN interface.  
**Likelihood:** Medium — automated scanners constantly probe residential IPs.  
**Mitigation:** CG-NAT from Virgin Plus means the router's WAN IP (192.168.2.43) is not directly reachable from the internet. The Hub 4000 provides an additional NAT layer. ER605 does not expose management UI to WAN by default.  
**Residual Risk:** Low

---

### 4. Rogue Device on Trusted Network
**Threat:** An unauthorised device connects to HomeNetwork WiFi and gains trusted access.  
**Likelihood:** Low  
**Mitigation:** WPA2-PSK on HomeNetwork WiFi. Password not shared with guests (separate Guest_WiFi SSID). Future improvement: 802.1X certificate-based auth.  
**Residual Risk:** Low–Medium

---

### 5. Management Interface Exposure
**Threat:** An attacker gains access to the router, switch, or AP management UI.  
**Likelihood:** Low  
**Mitigation:** Switch (192.168.0.101) and AP (192.168.0.102) are on VLAN1, only reachable from Trusted zone via explicit firewall rule. Router UI (192.168.10.1) only reachable from VLAN10. Default passwords changed on all devices.  
**Residual Risk:** Low

---

## Residual Risks and Future Improvements

| Risk | Improvement |
|------|-------------|
| WPA2-PSK shared secret | Migrate to WPA3 or 802.1X where supported |
| No IDS/IPS | Add network monitoring (e.g. Suricata, Zeek) |
| IoT device firmware | Regular firmware audit and update schedule |
| Tailscale coordination server | Evaluate self-hosted Headscale for full control |
| No VLAN10 egress filtering | Add outbound firewall rules to limit trusted devices to needed ports only |

---

## Summary

The network applies defence-in-depth with four isolated zones, enforced by hardware firewall rules at the router. The primary attack vectors (compromised IoT devices, malicious guests) are mitigated by VLAN segmentation. The CG-NAT layer from the ISP provides additional protection against direct internet-facing attacks.
