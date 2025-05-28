# n8n on Kubernetes

This repository contains Kubernetes manifests to deploy [n8n](https://n8n.io/), a workflow automation tool, on a Kubernetes cluster.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured to access your cluster
- Helm (optional, for Helm chart deployment)
- Ingress controller (optional, for external access)

## Deployment

### 1. Create Namespace

```bash
kubectl apply -f n8n-namespace.yaml
kubectl apply -f n8n-pvc.yaml
kubectl apply -f n8n-deployment.yaml
kubectl apply -f n8n-service.yaml
kubectl apply -f n8n-ingress.yaml

```

### 2. Security Considerations

Enable authentication in n8n configuration
Configure TLS for your ingress
Set resource limits for the n8n container
Regularly update to the latest n8n version
License

This project is licensed under the MIT License.

