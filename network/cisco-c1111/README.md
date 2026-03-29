# Cisco C1111-8P — Router Configuration

## Device Overview

| Item | Value |
|------|-------|
| Model | Cisco C1111-8P |
| IOS Version | IOS-XE 16.6.4 (Everest) |
| Hostname | C1111-HOME |
| WAN | Static IP on upstream ISP subnet |
| LAN Ports | 8x Gigabit Ethernet (switch module) |
| WAN Ports | 2x Gigabit Ethernet |

---

## Configuration Files

| File | Description |
|------|-------------|
| [running-config.md](running-config.md) | Full sanitized running configuration |
| [docs/vlan-design.md](docs/vlan-design.md) | VLAN segmentation design and rationale |
| [docs/zbf-config.md](docs/zbf-config.md) | Zone Based Firewall configuration |
| [docs/vpn-config.md](docs/vpn-config.md) | IKEv2 IPSec VPN server configuration |
| [docs/ddns-setup.md](docs/ddns-setup.md) | Dynamic DNS setup with No-IP |

---

## Network Diagram

![Network Diagram](network-diagram.png)

---

## Quick Reference

### SSH Access
```
ssh admin@<ROUTER-IP>
```

### Useful Show Commands
```
show ip interface brief       # Interface status and IPs
show zone security            # ZBF zone assignments
show zone-pair security       # ZBF zone pair policies
show crypto ikev2 sa          # Active VPN sessions
show ip dhcp bindings         # DHCP leases
show arp                      # ARP table
show ip nat translations      # Active NAT sessions
show version                  # IOS version and uptime
```
