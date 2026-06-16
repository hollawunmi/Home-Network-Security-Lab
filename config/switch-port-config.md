# Switch Port Configuration — TL-SG108E

## Device Info

- **Model:** TP-Link TL-SG108E
- **Management IP:** 192.168.0.101 (static, VLAN1)
- **Default Gateway:** 192.168.0.1

## 802.1Q VLAN Configuration

| VLAN ID | Name | Tagged Ports | Untagged Ports |
|---------|------|--------------|----------------|
| 1 | Default | Port 1 | Ports 2–8 |
| 10 | Trusted | Port 1, 7 | Port 3 |
| 20 | IoT | Port 1, 7 | Ports 4, 5, 6 |
| 30 | Guest | Port 1, 7 | — |

## PVID Settings

| Port | PVID | Connected Device |
|------|------|-----------------|
| Port 1 | 1 | ER605 router (trunk uplink) |
| Port 2 | 1 | Spare (VLAN1 access) |
| Port 3 | 10 | Laptop JOSHUA (Trusted) |
| Port 4 | 20 | Android TV (IoT) |
| Port 5 | 20 | Home TV (IoT) |
| Port 6 | 20 | PS5 (IoT) |
| Port 7 | 1 | PoE injector → EAP245 AP (trunk) |
| Port 8 | 1 | Spare (VLAN1 access) |

## Design Notes

**Port 1 (Trunk to ER605):** Tagged for all VLANs. Carries all VLAN traffic between switch and router.

**Port 7 (Trunk to EAP245):** Tagged for VLANs 1, 10, 20, 30. The AP uses these tags to map each SSID to the correct VLAN.

**Access ports (3, 4, 5, 6):** Untagged. End devices on these ports send and receive untagged frames; the switch applies the PVID tag internally.

**Spare ports (2, 8):** VLAN1 untagged. Used for management access when a physical cable is needed to reach the switch or AP UI.
