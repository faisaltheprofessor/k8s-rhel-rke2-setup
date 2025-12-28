# Infrastructure & OS Preparation

Target OS: AlmaLinux 9 (RHEL-compatible)

## Disable swap
```bash
swapoff -a
sed -i '/\sswap\s/d' /etc/fstab
```

## Kernel modules
```bash
cat <<EOF >/etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
modprobe overlay
modprobe br_netfilter
```

## Sysctl
```bash
cat <<EOF >/etc/sysctl.d/99-kubernetes.conf
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
EOF
sysctl --system
```

## SELinux / Firewall (homelab)
```bash
setenforce 0 || true
systemctl disable --now firewalld
```
