# VPN Configuration — WireGuard

## Server Configuration

| Setting | Value |
|---------|-------|
| Server interface | HomeVPN |
| Server tunnel IP | 10.0.0.1/24 |
| Listen port | UDP 51820 |
| Hosted on | TP-Link ER605 |

## Peer Configuration

| Setting | Value |
|---------|-------|
| Peer name | Josh_Phone |
| Peer tunnel IP | 10.0.0.2/32 |

## Port Forwarding

Configured on the Sagemcom Hub 4000:

| Protocol | External Port | Internal IP | Internal Port |
|----------|---------------|-------------|---------------|
| UDP | 51820 | 192.168.2.43 (ER605 WAN) | 51820 |

## CG-NAT Constraint

Virgin Plus uses CG-NAT. The Hub 4000 WAN IP (10.133.4.138) is a private address on a shared carrier network — inbound connections from the public internet cannot reach the router directly even with port forwarding configured.

**Solution:** Tailscale is used for remote access. Tailscale acts as a relay (DERP server) that allows the VPN tunnel to be established without requiring a publicly routable IP.

## WireGuard vs Tailscale

| Feature | WireGuard (direct) | Tailscale |
|---------|-------------------|-----------|
| Works with CG-NAT | ❌ | ✅ |
| Encrypted tunnel | ✅ | ✅ (uses WireGuard underneath) |
| Setup complexity | Medium | Low |
| Self-hosted | ✅ | Partially (coordination server is Tailscale) |

## Why WireGuard Was Still Configured

The WireGuard server on the ER605 remains active. If Virgin Plus ever removes CG-NAT or provides a public IP, direct WireGuard connections will work immediately with no changes needed.
