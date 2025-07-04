# Kubernetes Learning Note

## 1. Kubernetes Architecture
- **Master Node**: Controls the Kubernetes cluster, managing the API server, scheduler, and controller manager.
- **Worker Node**: Runs the applications in containers, managed by the kubelet and kube
proxy.
- **Pod**: The smallest deployable unit in Kubernetes, which can contain one or more containers.
- **Service**: An abstraction that defines a logical set of Pods and a policy to access them.
- **Namespace**: A way to divide cluster resources between multiple users or teams



## 2. Installation k0s
### On the Master Node
```bash
curl -sSL https://get.k0s.sh | sudo sh
sudo k0s install controller
sudo k0s start
```
