# k8s-rhel-rke2-setup

This repository documents a **production-style Kubernetes homelab** that I implemented and run in my own home lab.

It is built with:

- RKE2 (Rancher Kubernetes Engine v2)
- AlmaLinux 9 (RHEL-compatible)
- HAProxy + Keepalived for high availability
- Traefik as the Ingress Controller
- An internal Certificate Authority (CA) for TLS

The setup intentionally favors **boring, explicit, and reproducible** infrastructure — using predictable, well-understood components instead of clever or opaque solutions.

## 🧱 Architecture Overview

```text
Kubernetes API traffic

ADMIN / CLI
kubectl / rke2 join
  |
  v
k8s-api.home (VIP 192.168.178.210)
  |
  v
Load Balancers (lb1 / lb2)
Keepalived + HAProxy
  |
  v
Control Plane Nodes
- cp1 192.168.178.160
- cp2 192.168.178.161
- cp3 192.168.178.162


Application traffic

USER / BROWSER / CURL
https://*.apps.home
  |
  v
*.apps.home (VIP 192.168.178.211)
  |
  v
Load Balancers (lb1 / lb2)
Keepalived + HAProxy
  |
  v
Worker Nodes
- w1 192.168.178.165
- w2 192.168.178.166
- w3 192.168.178.167
- w4 192.168.178.168

Traefik runs as a DaemonSet on all workers
(hostPorts 80 / 443)
  |
  v
Kubernetes Services -> Application Pods
```