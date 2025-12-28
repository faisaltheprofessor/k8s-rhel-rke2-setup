# Architecture Overview

This homelab implements a highly available Kubernetes cluster using proven on‑prem patterns.

## Core components
- RKE2 Kubernetes distribution
- HAProxy + Keepalived for Virtual IPs
- Traefik as Ingress Controller
- Internal CA for HTTPS

## Nodes

Control-plane: cp1, cp2, cp3  
Workers: w1, w2, w3, w4  
Load balancers: lb1, lb2  

## VIPs

- k8s-api.home → 192.168.178.210
- *.apps.home  → 192.168.178.211

VIPs are used **only for services**, never for SSH.
