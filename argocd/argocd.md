# Argo CD
Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes. It allows you to manage your Kubernetes resources using Git as the source of truth.
## Installation

### On the Master Node
```bash
# using helm
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argo-cd argo/argo-cd --namespace argocd --create-namespace
# check the status
kubectl get pods -n argocd
```

## ✅ Public Argo CD via Cloudflare Tunnel + Traefik (Kubernetes)

Final architecture:

```bash
User → Cloudflare DNS → Cloudflared Tunnel (outside K8s) → Traefik Ingress → Argo CD Server
```

### Objectives
Expose the Argo CD UI publicly using:
- Traefik as ingress controller
- Cloudflared tunnel (running outside the cluster)
- No public LoadBalancer or NodePort
- Domain: https://argocd.example.com

### Set up

Cloudflare Tunnel:
```yaml
- hostname: argocd.example.com
  service: http://10.99.192.190:80 # Replace with your cluster IP of Argo CD server
  originRequest:
    httpHostHeader: argocd.example.com
```

Ingress

```yaml
annotations:
  traefik.ingress.kubernetes.io/router.entrypoints: web
  traefik.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
    - host: argocd.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: argo-cd-argocd-server
                port:
                  number: 80
```


### Error I had

### ❌ Error 1: Cloudflared can't resolve `traefik.traefik`

Error message:
``` bash
lookup traefik.traefik on 127.0.0.53:53: no such host
```
Cause:
I was running cloudflared outside the cluster, and it couldn't resolve the internal DNS name `traefik.traefik`.

Fix:
I changed the tunnel configuration to use the cluster IP of the Traefik service instead of the DNS name.
```yaml
service: http://10.99.192.190:80 # Replace with your cluster IP of Traefik service
``` 

### ❌ Error 2: Cloudflare 502 Bad Gateway

Error message:
```bash
502 Bad Gateway – Unable to reach the origin service
```

Cause:
Cloudflared couldn’t reach the Traefik service because of the unresolved DNS name `traefik.traefik`.

Fix:
Used Traefik’s ClusterIP directly, confirmed connectivity from the host.name.

### ❌ Error 3: Browser shows ERR_TOO_MANY_REDIRECTS
Cause:
 - Argo CD redirects HTTP to HTTPS by default.
 - But Cloudflared was sending HTTP.
 - This caused a redirect loop: HTTP → HTTPS → HTTP again.
Fix:
I patched the argo-cd-argocd-server deployment to include:

```yaml
args:
    - --insecure
```
This disables HTTPS enforcement in Argo CD and allows serving plain HTTP.