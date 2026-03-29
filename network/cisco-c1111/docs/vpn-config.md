# IKEv2 IPSec VPN Server

## Overview

An IKEv2 IPSec VPN server is configured on the Cisco C1111-8P to allow secure remote access from phones and laptops. The VPN uses industry-standard AES-256 encryption with SHA256 integrity and Diffie-Hellman Group 14 for key exchange.

## VPN Specifications

| Component | Value |
|-----------|-------|
| Protocol | IKEv2 |
| Encryption | AES-CBC-256 |
| Integrity | SHA256 |
| PRF | SHA256 |
| DH Group | Group 14 (2048-bit MODP) |
| Authentication | Pre-shared key |
| Tunnel Type | Virtual Tunnel Interface (VTI) |
| Dead Peer Detection | 30s interval / 5 retries |

## Required Port Forwarding

| Port | Protocol | Purpose |
|------|----------|---------|
| 500 | UDP | IKEv2 key exchange (ISAKMP) |
| 4500 | UDP | IKEv2 NAT traversal |

## Client Configuration

### iOS / macOS
1. Settings → VPN → Add VPN Configuration
2. Type: IKEv2
3. Server: `<YOUR-DDNS-HOSTNAME>`
4. Remote ID: `<YOUR-DDNS-HOSTNAME>`
5. Authentication: Username
6. Pre-shared key: `<YOUR-PSK>`

### Windows (Built-in)
```powershell
Add-VpnConnection -Name "Homelab VPN" `
  -ServerAddress "<YOUR-DDNS-HOSTNAME>" `
  -TunnelType IKEv2 `
  -AuthenticationMethod MachineCertificate `
  -Force

Set-VpnConnectionIPsecConfiguration `
  -ConnectionName "Homelab VPN" `
  -AuthenticationTransformConstants SHA256128 `
  -CipherTransformConstants AES256 `
  -EncryptionMethod AES256 `
  -IntegrityCheckMethod SHA256 `
  -DHGroup Group14 `
  -PfsGroup None `
  -Force
```

### Linux (strongSwan)
```bash
sudo apt install strongswan strongswan-pki libcharon-extra-plugins
```

Config file `/etc/ipsec.conf`:
```
conn homelab-vpn
  keyexchange=ikev2
  right=<YOUR-DDNS-HOSTNAME>
  rightid=<YOUR-DDNS-HOSTNAME>
  rightsubnet=0.0.0.0/0
  left=%defaultroute
  leftid=%any
  authby=psk
  auto=start
```

## Architecture Notes

- A **Virtual Template Interface** (Virtual-Template1) is used to dynamically create tunnel interfaces for each connecting client
- The tunnel interface is marked as `ip nat inside` so VPN clients can access the internet through the router's NAT
- The `ip unnumbered` command borrows the WAN IP rather than requiring a dedicated IP for the tunnel interface
- Dead Peer Detection (DPD) automatically cleans up stale tunnel sessions after 5 failed keepalives
