# k8s-rhel-rke2

**Highly available Kubernetes homelab using RKE2 on RHEL-compatible Linux**

This repository documents a **production-style Kubernetes homelab** that I implemented and run in my own home lab.

It is built with:

- RKE2 (Rancher Kubernetes Engine v2)
- AlmaLinux 9 (RHEL-compatible)
- HAProxy + Keepalived
- Traefik Ingress Controller
- An internal Certificate Authority (CA)

The setup intentionally favors **boring, explicit, and reproducible** infrastructure—using predictable, well‑understood components instead of clever or opaque solutions.

---

## ✨ Highlights

- 3× control-plane nodes (etcd embedded)
- 4× worker nodes
- 2× load balancers with floating VIPs
- Highly available Kubernetes API
- Highly available ingress
- HTTPS everywhere using an internal CA
- No cloud dependencies

---

## 🧱 Architecture Overview

                         Kubernetes API traffic
----------------------

ADMIN / CLI
kubectl / rke2 join
        |
        v
k8s-api.home (VIP 192.168.178.210)
        |
        v
+-----------------------------+
| Load Balancers (VIP HA)     |
| lb1 / lb2                   |
| Keepalived + HAProxy        |
+-----------------------------+
        |
        v
+-------------+-------------+-------------+
|     cp1     |     cp2     |     cp3     |
| 192.168.178.160  .161  .162 |
| control-plane / etcd       |
+-------------+-------------+-------------+


Application traffic
-------------------

USER / BROWSER / CURL
https://whoami.apps.home
        |
        v
*.apps.home (VIP 192.168.178.211)
        |
        v
+-----------------------------+
| Load Balancers (VIP HA)     |
| lb1 / lb2                   |
| Keepalived + HAProxy        |
+-----------------------------+
        |
        v
+---------+---------+---------+---------+
|   w1    |   w2    |   w3    |   w4    |
| .165    | .166    | .167    | .168    |
| Traefik DaemonSet (hostPorts 80/443)  |
+---------+---------+---------+---------+
        |
        v
Kubernetes Services → Application Pods