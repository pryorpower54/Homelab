# Kubernetes Cluster (K3s)

## Overview

A lightweight Kubernetes cluster is being built using K3s across the Lab VLAN. K3s is a fully compliant Kubernetes distribution designed for resource-constrained environments — perfect for home lab use while teaching the same concepts as full Kubernetes.

## Cluster Design

| Node | Hardware | Role | IP | Status |
|------|----------|------|----|--------|
| k3s-master | Proxmox VM (Ubuntu 22.04) | Control plane | 192.168.10.3 | Planned |
| k3s-worker-1 | Raspberry Pi 4 | Worker node | 192.168.10.4 | Planned |
| k3s-worker-2 | Mini PC (Beelink EQ12) | Worker node | 192.168.10.5 | Planned |
| k3s-worker-3 | Mini PC (Beelink EQ12) | Worker node | 192.168.10.6 | Planned |

## Why K3s

- Lightweight — runs comfortably on Raspberry Pi
- Fully Kubernetes compliant — same `kubectl` commands
- Single binary install — much simpler than full K8s
- Built-in Traefik ingress controller
- Built-in local storage provisioner

## Planned Workloads

| Application | Purpose |
|-------------|---------|
| Traefik | Ingress controller |
| cert-manager | TLS certificate management |
| Wazuh | SIEM and log aggregation |
| Grafana + Prometheus | Monitoring and metrics |
| Portainer | Container management UI |
| Pi-hole | DNS-based ad and tracker blocking |

## Network Design

All cluster nodes are connected to the Lab VLAN (192.168.10.0/24) on the Cisco C1111-8P. The Cisco router handles all routing, NAT, and firewall policies for cluster traffic.

```
Lab VLAN (192.168.10.0/24)
├── 192.168.10.1  — Cisco C1111-8P (gateway)
├── 192.168.10.2  — Proxmox hypervisor
├── 192.168.10.3  — K3s control plane (VM on Proxmox)
├── 192.168.10.4  — K3s worker 1 (Raspberry Pi 4)
├── 192.168.10.5  — K3s worker 2 (Mini PC)
└── 192.168.10.6  — K3s worker 3 (Mini PC)
```

## Installation (Planned)

### Control Plane
```bash
curl -sfL https://get.k3s.io | sh -
```

### Worker Nodes
```bash
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.10.3:6443 K3S_TOKEN=<NODE-TOKEN> sh -
```

### Verify Cluster
```bash
kubectl get nodes
kubectl get pods --all-namespaces
```

## Status

- [ ] Procure mini PCs
- [ ] Deploy control plane VM on Proxmox
- [ ] Configure Raspberry Pi as worker node
- [ ] Install K3s on all nodes
- [ ] Deploy Traefik ingress
- [ ] Deploy monitoring stack (Grafana + Prometheus)
- [ ] Deploy Wazuh SIEM
