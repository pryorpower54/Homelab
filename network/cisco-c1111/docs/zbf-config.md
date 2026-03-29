# Zone Based Firewall (ZBF)

## Overview

The Zone Based Firewall (ZBF) is a stateful firewall model built into Cisco IOS-XE that controls traffic flow between network segments. Unlike traditional ACLs, ZBF uses a default-deny model — any two zones without an explicit policy cannot communicate at all.

## Why ZBF over ACLs

| Feature | ACL | ZBF |
|---------|-----|-----|
| Stateful inspection | No | Yes |
| Default deny between zones | No | Yes |
| Session tracking | No | Yes |
| Scalability | Limited | High |
| Self zone protection | No | Yes |

## Zone Definitions

| Zone | Interface | Trust Level |
|------|-----------|-------------|
| WAN | GigabitEthernet0/0/0 | Untrusted |
| Lab | Vlan10 | Trusted |
| Pentest | Vlan20 | Isolated |
| IoT | Vlan30 | Low Trust |
| Management | Vlan99 | Trusted |
| self | Router itself | System defined |

## Traffic Policy Matrix

| Source | Destination | Policy | Action |
|--------|-------------|--------|--------|
| Lab | WAN | PM-LAB-TO-WAN | ✅ Inspect (internet access) |
| Management | WAN | PM-LAB-TO-WAN | ✅ Inspect (internet access) |
| IoT | WAN | PM-IOT-TO-WAN | ✅ Inspect (internet only) |
| Pentest | WAN | PM-IOT-TO-WAN | ✅ Inspect (internet only) |
| Lab | Management | PM-LAB-TO-LAB | ✅ Inspect (peer access) |
| Management | Lab | PM-LAB-TO-LAB | ✅ Inspect (peer access) |
| self | WAN | PM-LAB-TO-WAN | ✅ Inspect (router outbound) |
| WAN | self | PM-WAN-TO-SELF | ✅ Inspect (VPN + SSH only) |
| WAN | Lab | PM-DROP-ALL | ❌ Drop |
| WAN | Management | PM-DROP-ALL | ❌ Drop |
| WAN | IoT | PM-DROP-ALL | ❌ Drop |
| WAN | Pentest | PM-DROP-ALL | ❌ Drop |

## ACL-VPN (Permitted Inbound Traffic)

The following traffic is permitted inbound from the WAN zone to the router's self zone:

| Protocol | Port | Purpose |
|----------|------|---------|
| UDP | 500 | IKEv2 key exchange |
| UDP | 4500 | IKEv2 NAT traversal |
| ESP | — | IPSec tunnel traffic |
| TCP | 22 | SSH management |
| ICMP | — | From upstream subnet only |

## Key Design Decisions

- **Pentest VLAN is fully isolated** — no zone pair exists between Pentest and any internal zone other than WAN. Even if a pentesting tool is compromised or misbehaves it cannot reach lab or management resources.
- **self-to-WAN zone pair** — required to allow the router itself to initiate outbound connections for DDNS updates, NTP, and ping. Without this the router's own traffic is blocked.
- **Stateful inspection (inspect) over pass** — using `inspect` instead of `pass` ensures return traffic is automatically permitted and TCP sessions are properly tracked.
