# 🏠 Home Lab

A continuously evolving home lab environment built to develop hands-on experience in enterprise networking, cybersecurity, virtualization, and container orchestration. This lab is the foundation for a future cybersecurity firm focused on providing enterprise-grade security services to small and mid-sized businesses.

---

## 🌐 Network Infrastructure

### Cisco C1111-8P ISR Router
The core of the lab network is a Cisco C1111-8P running IOS-XE 16.6.4, configured with enterprise-grade features including VLAN segmentation, Zone Based Firewall, NAT, Dynamic DNS, and an IKEv2 IPSec VPN server.

**Key configurations:**
- 4 isolated VLANs segmented by trust level
- Zone Based Firewall (ZBF) with stateful inspection
- IKEv2 IPSec VPN server with AES-256 / SHA256 / Group 14 (Deprecated)
- Dynamic DNS via No-IP for remote VPN access
- SSH remote management (SSHv2 only)
- NAT/PAT for internet access across all VLANs

### Network Topology

| VLAN | Name | Subnet | Purpose |
|------|------|--------|---------|
| 10 | Lab | 192.168.10.0/24 | Trusted lab devices and VMs |
| 20 | Pentest | 192.168.20.0/24 | Isolated pentesting environment |
| 30 | IoT | 192.168.30.0/24 | Low trust IoT devices |
| 99 | Management | 192.168.99.0/24 | Network infrastructure management |

### Zone Based Firewall Design

| Zone | Trust Level | Policy |
|------|-------------|--------|
| WAN | Untrusted | Inbound blocked by default |
| Lab | Trusted | Internet + peer access |
| Pentest | Isolated | Internet only — no inter-VLAN |
| IoT | Low Trust | Internet only |
| Management | Trusted | Internet + Lab access |

### Network Diagram
![Network Diagram](network/cisco-c1111/network-diagram.png)

---

## 🖥️ Virtualization

### Proxmox VE
A Proxmox hypervisor is deployed on the Lab VLAN (192.168.10.0/24) for running virtual machines and containers. Current and planned workloads include:

- Parrot OS (pentesting)
- Windows Server (Active Directory lab)
- Kubernetes worker nodes
- Vulnerable VMs for practice (Metasploitable, DVWA)

---

## ☸️ Kubernetes Cluster (In Progress)

A multi-node Kubernetes cluster is being built using K3s across the Lab VLAN:

| Node | Hardware | Role |
|------|----------|------|
| Control Plane | Proxmox VM | Master node |
| Worker 1 | Raspberry Pi 4 | Worker node |
| Worker 2 | Mini PC (planned) | Worker node |
| Worker 3 | Mini PC (planned) | Worker node |

---

## 🔐 Security Focus

This lab is purpose-built around security principles:

- **Network segmentation** — traffic isolated by trust level using VLANs and ZBF
- **Least privilege** — each zone only accesses what it absolutely needs
- **Implicit deny** — anything not explicitly permitted is blocked by default
- **Stateful inspection** — ZBF tracks connection state rather than just packets
- **Encrypted remote access** — IKEv2 IPSec VPN with AES-256 encryption
- **Pentest isolation** — dedicated VLAN with zero access to other segments

---

## 🗺️ Roadmap

- [x] Cisco C1111-8P initial configuration
- [x] VLAN segmentation (4 VLANs)
- [x] Zone Based Firewall
- [x] IKEv2 IPSec VPN server
- [x] Dynamic DNS (No-IP)
- [x] Proxmox hypervisor deployment
- [ ] Kubernetes cluster (K3s)
- [ ] Quality of Service (QoS) configuration
- [ ] Active Directory lab environment
- [ ] SIEM / log aggregation (Wazuh or Splunk)
- [ ] IDS/IPS deployment
- [ ] VPN client testing and documentation
- [ ] 802.1X port authentication

---

## 🎯 Goal

This home lab is the technical foundation for a future cybersecurity firm focused on delivering enterprise-grade network security, penetration testing, and managed security services to small and mid-sized businesses that are typically underserved and underprotected.

---

## 📁 Repository Structure
```
homelab/
├── README.md
├── network/
│   └── cisco-c1111/
│       ├── README.md
│       ├── running-config.md
│       ├── network-diagram.png
│       └── docs/
│           ├── vlan-design.md
│           ├── zbf-config.md
│           ├── vpn-config.md
│           └── ddns-setup.md
├── proxmox/
│   └── README.md
├── kubernetes/
│   └── README.md
└── docs/
    └── network-diagram.png
```

---

> ⚠️ All sensitive values (IP addresses, credentials, pre-shared keys, hostnames) have been replaced with placeholders throughout this repository.
