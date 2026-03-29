# Proxmox VE — Hypervisor

## Overview

A Proxmox VE hypervisor is deployed on the Lab VLAN (192.168.10.0/24) as the virtualization platform for the home lab. Proxmox provides both KVM virtual machines and LXC containers from a single web-based interface.

## Hardware

| Component | Details |
|-----------|---------|
| Form Factor | Laptop |
| Network | Connected to Lab VLAN (Gi0/1/0) |
| IP Address | 192.168.10.2 (static) |
| Web UI | https://192.168.10.2:8006 |

## Network Configuration

```
# /etc/network/interfaces
auto lo
iface lo inet loopback

auto vmbr0
iface vmbr0 inet static
    address 192.168.10.2/24
    gateway 192.168.10.1
    dns-nameservers 8.8.8.8 8.8.4.4
    bridge-ports <physical-interface>
    bridge-stp off
    bridge-fd 0
```

## Current VMs / Containers

| Name | OS | Role | VLAN |
|------|----|------|------|
| parrot-os | Parrot OS | Pentesting workstation | Lab (10) |

## Planned VMs

| Name | OS | Role | VLAN |
|------|----|------|------|
| win-server | Windows Server 2022 | Active Directory lab | Lab (10) |
| k3s-master | Ubuntu 22.04 | Kubernetes control plane | Lab (10) |
| vuln-lab | Metasploitable / DVWA | Vulnerable practice targets | Pentest (20) |

## Kubernetes Cluster

Proxmox hosts the Kubernetes control plane VM. Worker nodes run on dedicated hardware (Raspberry Pi and mini PCs) also connected to the Lab VLAN. See [kubernetes/README.md](../../kubernetes/README.md) for details.

## Accessing the Web UI

The Proxmox web UI is only accessible from the Lab VLAN (192.168.10.0/24) or via VPN. Connect to the homelab VPN first if accessing remotely, then navigate to:

```
https://192.168.10.2:8006
```

> Note: Proxmox uses a self-signed certificate by default — accept the browser warning to proceed.
