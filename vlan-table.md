# VLAN Configuration

## ER605 Router VLAN Table

| VLAN ID | Name | Subnet | Gateway | DHCP Range | Purpose |
|---------|------|--------|---------|------------|---------|
| 1 | LAN | 192.168.0.0/24 | 192.168.0.1 | Static only | Management (switch, AP) |
| 10 | Trusted | 192.168.10.0/24 | 192.168.10.1 | .100–.200 | Family devices |
| 20 | IoT_Isolated | 192.168.20.0/24 | 192.168.20.1 | .100–.200 | IoT devices |
| 30 | Guest_WiFi | 192.168.30.0/24 | 192.168.30.1 | .100–.200 | Guest devices |

## Router Access

| Interface | IP | Accessible From |
|-----------|-----|----------------|
| VLAN10 interface | 192.168.10.1 | HomeNetwork WiFi, Port 3 |
| VLAN1 interface | 192.168.0.1 | Port 2 cable |

## Design Rationale

**VLAN1 (Management):** Kept separate from user traffic. Switch (192.168.0.101) and AP (192.168.0.102) live here. A firewall permit rule allows Trusted → Management so the network admin can manage devices from HomeNetwork WiFi without requiring a physical cable.

**VLAN10 (Trusted):** All family-owned devices with full internet access. Cannot be reached by IoT or Guest devices.

**VLAN20 (IoT_Isolated):** Smart home and entertainment devices. Internet access only. Isolated from Trusted and Guest zones to contain any compromise.

**VLAN30 (Guest_WiFi):** Visitor devices. Internet access only via captive portal. 5GHz only to limit physical signal range. Isolated from all internal zones.
