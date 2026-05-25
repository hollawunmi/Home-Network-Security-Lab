# Verification Test Results

**Date:** May 2026  
**Tester:** Segun Olawunmi  

---

## Test Environment

| Device | VLAN | IP |
|--------|------|----|
| Laptop JOSHUA | VLAN10 (Trusted) | 192.168.10.104 |
| Samsung S25 Ultra (test device) | VLAN20 (IoT) | 192.168.20.100 |
| Samsung S25 Ultra (test device) | VLAN30 (Guest) | 192.168.30.101 |

Tool used for mobile pings: **Ping & Net** (Android)

---

## Test Results

### Test 1 — Trusted → Internet
**From:** Laptop JOSHUA (192.168.10.104)  
**To:** 8.8.8.8 (Google DNS)  
**Expected:** ✅ Success  
**Result:** ✅ Pass — 4/4 packets received, avg 12ms latency

```
Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: bytes=32 time=12ms TTL=115
Reply from 8.8.8.8: bytes=32 time=13ms TTL=115
Reply from 8.8.8.8: bytes=32 time=13ms TTL=115
Reply from 8.8.8.8: bytes=32 time=13ms TTL=115
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

---

### Test 2 — IoT → Trusted (should be blocked)
**From:** Phone on IoT_Isolated (192.168.20.100)  
**To:** Laptop JOSHUA (192.168.10.104)  
**Expected:** ❌ Blocked  
**Result:** ✅ Pass — 100% packet loss

```
PING 192.168.10.104 — 3 packets transmitted, 0 received, 100% packet loss
```

Firewall rule enforced: **Block_IoT_to_Trusted**

---

### Test 3 — Guest → IoT (should be blocked)
**From:** Phone on Guest_WiFi (192.168.30.101)  
**To:** IoT subnet (192.168.20.104)  
**Expected:** ❌ Blocked  
**Result:** ✅ Pass — 100% packet loss

```
PING 192.168.20.104 — 3 packets transmitted, 0 received, 100% packet loss
```

Firewall rule enforced: **Block_Guest_to_IoT**

---

## Summary

| Test | Pass/Fail |
|------|-----------|
| Trusted → Internet | ✅ Pass |
| IoT → Trusted | ✅ Blocked |
| Guest → IoT | ✅ Blocked |

All VLAN isolation policies verified and working as designed.
