# VLAN Design

## Overview

The network is segmented into four VLANs to isolate traffic by trust level and function. This follows a defense-in-depth approach where each segment only has access to what it absolutely needs.

## VLAN Table

| VLAN | Name | Subnet | Gateway | Ports | Trust Level |
|------|------|--------|---------|-------|-------------|
| 10 | Lab | 192.168.10.0/24 | 192.168.10.1 | Gi0/1/0-2 | Trusted |
| 20 | Pentest | 192.168.20.0/24 | 192.168.20.1 | Gi0/1/3-4 | Isolated |
| 30 | IoT | 192.168.30.0/24 | 192.168.30.1 | Gi0/1/5-6 | Low Trust |
| 99 | Management | 192.168.99.0/24 | 192.168.99.1 | Gi0/1/7 | Trusted |

## DHCP Pools

Each VLAN has a dedicated DHCP pool. Addresses `.1` through `.10` are reserved for static assignments.

| Pool | DHCP Range | DNS |
|------|------------|-----|
| Lab | 192.168.10.11 – 192.168.10.254 | 8.8.8.8 / 8.8.4.4 |
| Pentest | 192.168.20.11 – 192.168.20.254 | 8.8.8.8 / 8.8.4.4 |
| IoT | 192.168.30.11 – 192.168.30.254 | 8.8.8.8 / 8.8.4.4 |
| Management | 192.168.99.11 – 192.168.99.254 | 8.8.8.8 / 8.8.4.4 |

## Static IP Reservations

| Device | IP | VLAN |
|--------|----|------|
| Proxmox Hypervisor | 192.168.10.2 | Lab |

## Design Rationale

### Lab VLAN (10)
The primary work area for VMs, containers, and lab devices. Trusted zone with internet access and communication with the Management VLAN. Houses the Proxmox hypervisor and Kubernetes cluster nodes.

### Pentest VLAN (20)
Completely isolated from all other VLANs. Has internet access only. Pentesting tools like Parrot OS run here — this ensures that any aggressive scanning, exploitation, or testing activity cannot affect other network segments.

### IoT VLAN (30)
IoT devices are notoriously insecure and often compromised. Isolating them on a dedicated VLAN with internet-only access prevents a compromised IoT device from pivoting to lab or management resources.

### Management VLAN (99)
Reserved for network infrastructure management traffic. Can communicate with the Lab VLAN for administrative purposes but is isolated from Pentest and IoT.

## Security Principles Applied

- **Network Segmentation** — traffic isolated by trust level
- **Least Privilege** — each zone only accesses what it needs
- **Implicit Deny** — anything not explicitly permitted is blocked
- **Defense in Depth** — multiple layers of isolation
