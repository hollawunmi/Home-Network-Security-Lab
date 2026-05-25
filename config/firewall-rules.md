# Firewall Rules

## ER605 Access Control Rules

All rules are LAN→LAN direction, Protocol ALL, IPv4.

| ID | Name | Policy | Source | Destination | Purpose |
|----|------|--------|--------|-------------|---------|
| 1 | Block_IoT_to_Trusted | Block | IoT_Isolated | Trusted | Prevents IoT devices reaching trusted network |
| 2 | Block_Guest_to_Trusted | Block | Guest_WiFi | Trusted | Prevents guest devices reaching trusted network |
| 3 | Block_Guest_to_IoT | Block | Guest_WiFi | IoT_Isolated | Prevents guest devices reaching IoT network |
| 4 | Block_IoT_to_Guest | Block | IoT_Isolated | Guest_WiFi | Prevents IoT devices reaching guest network |
| 5 | Allow_Trusted_to_Management | Allow | Trusted | LAN | Allows admin to manage switch/AP from WiFi |

## Security Model

The default inter-VLAN behaviour on the ER605 is to allow all LAN→LAN traffic unless explicitly blocked. Rules 1–4 enforce the isolation policy. Rule 5 is a deliberate exception allowing the Trusted zone (network admin) to reach management devices on VLAN1.

## Rule Order

Rules are evaluated top-down. The block rules (1–4) are listed before the allow rule (5) to ensure deny takes precedence where applicable.

## What Is Intentionally Allowed

- All VLANs → Internet (WAN): allowed by default
- Trusted → Management VLAN (192.168.0.x): allowed via Rule 5
- Trusted → Router (192.168.10.1): allowed by default

## What Is Intentionally Blocked

- IoT → Trusted: blocked (Rule 1)
- Guest → Trusted: blocked (Rule 2)
- Guest → IoT: blocked (Rule 3)
- IoT → Guest: blocked (Rule 4)
