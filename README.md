# k8s-rhel-rke2

**Highly available Kubernetes homelab using RKE2 on RHEL-compatible Linux**

This repository documents a **production-style Kubernetes homelab** built with:

- RKE2 (Rancher Kubernetes Engine v2)
- AlmaLinux 9 (RHEL-compatible)
- HAProxy + Keepalived
- Traefik Ingress Controller
- Internal Certificate Authority (CA)

The setup is intentionally **boring, explicit, and reproducible**.

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

```text
           +--------------------+
           |   k8s-api.home     |
           |   (VIP)            |
           +----------+---------+
                      |
               +------+------+
               |   HAProxy   |
               | lb1 / lb2   |
               +------+------+
                      |
        +-------------+-------------+
        |             |             |
      cp1           cp2           cp3
 (control-plane) (control-plane) (control-plane)

           +--------------------+
           |  *.apps.home       |
           |  (VIP)             |
           +----------+---------+
                      |
               +------+------+
               |   HAProxy   |
               | lb1 / lb2   |
               +------+------+
                      |
        +-------------+-------------+
        |      |      |      |      |
       w1     w2     w3     w4   (Traefik)