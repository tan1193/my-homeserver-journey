# Traefik

## Traefik
Traefik is a modern reverse proxy and load balancer that makes deploying microservices easy. It automatically discovers the right configuration for your services and provides features like load balancing, SSL termination, and more.
### Installation
#### On the Master Node
```bash
// using helm
helm repo add traefik https://helm.traefik.io/traefik
helm repo update
helm install traefik traefik/traefik --namespace kube-system --create-namespace \
  --set service.type=LoadBalancer \
  --set service.loadBalancerIP=<your-load-balancer-ip> \
  --set rbac.enabled=true \
  --set dashboard.enabled=true
```
