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

                         (ADMIN / CLI)
                      kubectl / rke2 join
                             |
                             v
                   DNS: k8s-api.home -> VIP 192.168.178.210
                             |
                             v
                 +---------------------------+
                 |  Load Balancers (VIP HA)  |
                 |  lb1 / lb2 (Keepalived)   |
                 |  HAProxy (TCP forward)    |
                 +-------------+-------------+
                               |
         +---------------------+---------------------+
         |                     |                     |
         v                     v                     v
   cp1 192.168.178.160   cp2 192.168.178.161   cp3 192.168.178.162
   (control-plane)        (control-plane)        (control-plane)
   API :6443              API :6443              API :6443
   RKE2:9345              RKE2:9345              RKE2:9345


                    (USER / BROWSER / CURL)
                 https://whoami.apps.home
                             |
                             v
                  DNS: *.apps.home -> VIP 192.168.178.211
                             |
                             v
                 +---------------------------+
                 |  Load Balancers (VIP HA)  |
                 |  lb1 / lb2 (Keepalived)   |
                 |  HAProxy (TCP forward)    |
                 +-------------+-------------+
                               |
             forwards to ANY worker on :80 / :443 (hostPorts)
                               |
         +---------------------+---------------------+---------------------+---------------------+
         |                     |                     |                     |
         v                     v                     v                     v
   w1 192.168.178.165     w2 192.168.178.166     w3 192.168.178.167     w4 192.168.178.168
   (worker node)          (worker node)          (worker node)          (worker node)
   Traefik pod            Traefik pod            Traefik pod            Traefik pod
   (DaemonSet)            (DaemonSet)            (DaemonSet)            (DaemonSet)
         |                     |                     |                     |
         +---------------------+---------------------+---------------------+
                               |
                               v
                 Kubernetes Services -> Application Pods
                 (routing based on Ingress host/path)