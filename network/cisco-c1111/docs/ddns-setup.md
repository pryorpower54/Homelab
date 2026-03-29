# Dynamic DNS Setup (No-IP)

## Overview

A Dynamic DNS (DDNS) hostname is configured to provide a stable, memorable address for VPN client connections. Since most residential ISPs assign dynamic public IP addresses that change periodically, DDNS ensures the VPN endpoint hostname always resolves to the current public IP.

## Provider

**No-IP** (noip.com) — free tier supports up to 3 hostnames with a 30-day confirmation requirement.

## How It Works

1. The Cisco router's built-in DDNS client monitors the WAN interface IP
2. When the IP changes, the router sends an HTTP update to No-IP's API
3. No-IP updates the DNS record for the hostname
4. VPN clients always connect to the hostname, not the raw IP

## Cisco DDNS Configuration

```
ip ddns update method NO-IP
 HTTP
  add http://<NOIP-USER>:<NOIP-KEY>@dynupdate.no-ip.com/nic/update?hostname=<h>&myip=<a>
 interval maximum 0 1 0 0
!
interface GigabitEthernet0/0/0
 ip ddns update hostname <YOUR-DDNS-HOSTNAME>
 ip ddns update NO-IP
```

## Verification

```
show ip ddns update
```

## Fallback — No-IP DUC Client

If the Cisco's built-in DDNS client fails to update (can happen with some ISP configurations), install the No-IP Dynamic Update Client (DUC) on a always-on Windows or Linux machine as a fallback:

- Download from: https://www.noip.com/download
- Runs as a background service
- Updates No-IP automatically when the public IP changes

## Notes

- The DDNS key is a one-time credential — regenerate it periodically for security
- No-IP free hostnames expire every 30 days if not confirmed — set a calendar reminder
- The hostname is used as both the VPN server address and the Remote ID in IKEv2 client configuration
