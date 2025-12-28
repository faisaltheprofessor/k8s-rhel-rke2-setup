# RKE2 Cluster Setup

## Bootstrap cp1
```yaml
node-ip: 192.168.178.160
cluster-init: true
```

## Join other nodes
```yaml
server: https://k8s-api.home:9345
token: <NODE_TOKEN>
node-ip: <NODE_IP>
```

Workers use rke2-agent instead of rke2-server.
