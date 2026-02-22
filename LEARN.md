# Kubernetes with Minikube and Docker

## Prerequisites

- Docker installed and running
- Minikube installed
- kubectl installed

## Installation

### Install Minikube

```bash
# macOS
brew install minikube

# Linux
curl -LO https://github.com/kubernetes/minikube/releases/download/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

## Getting Started (Brief overview)

### Start Minikube with Docker Driver

```bash
minikube start --driver=docker
```

### Verify Installation

```bash
minikube status
kubectl cluster-info
```

## Basic Commands

### Deploy an Application

```bash
kubectl create deployment hello --image=nginx
kubectl expose deployment hello --type=LoadBalancer --port=8080 --target-port=80
```

### View Resources

```bash
kubectl get pods
kubectl get services
minikube service hello  # Access the service
```

### Check Logs

```bash
kubectl logs <pod-name>
kubectl describe pod <pod-name>
```

## Useful Commands

```bash
minikube dashboard        # Open web dashboard
minikube stop             # Stop cluster
minikube delete           # Delete cluster
kubectl delete deployment hello  # Remove deployment
```

## Example

- Review [`mongo-deployment.yaml`](./mongo-deployment.yaml)
  Used to create a Deployment and Pod.
- `mongo-secret.yaml` is excluded from the repo (see `.gitignore`) and must be created manually.
  Sensitive values like username and password must be base64-encoded:

```sh
  echo -n "username" | base64
```

- Secrets must be applied before the Deployment, since it depends on them:

```sh
  kubectl apply -f mongo-secret.yaml
```

- Then create the Deployment:

```sh
  kubectl apply -f mongo-deployment.yaml
```

## Resources

- [Minikube Documentation](https://minikube.sigs.k8s.io/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
