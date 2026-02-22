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

## Deployment Guide: MongoDB with Mongo-Express

This project demonstrates a complete Kubernetes setup with MongoDB and a web-based MongoDB management UI (Mongo-Express).

### Project Architecture

- **MongoDB**: NoSQL database running in a Pod, exposed via a Kubernetes Service named `mongodb-service`
- **Mongo-Express**: Web UI (port 8081) for managing MongoDB, connects to MongoDB via service DNS
- **Secrets**: Stores sensitive credentials (username/password) - never commit to repo
- **ConfigMap**: Stores non-sensitive configuration (database service URL)

### Files Overview

| File | Purpose | Must Exist |
|------|---------|-----------|
| [`mongo-secret.yaml`](./mongo-secret.yaml) | Base64-encoded MongoDB root credentials | ✅ Yes (excluded from git) |
| [`mongo-deployment.yaml`](./mongo-deployment.yaml) | MongoDB Deployment + Service | ✅ Yes |
| [`mongo-config-map.yaml`](./mongo-config-map.yaml) | Database service URL configuration | ✅ Yes |
| [`mongo-express.yaml`](./mongo-express.yaml) | Mongo-Express Deployment + Service | ✅ Yes |

### Step-by-Step Deployment

#### 1. Create Secrets (if not exists)

MongoDB credentials must be base64-encoded:

```sh
# Encode your credentials
echo -n "username" | base64  # Output: dXNlcm5hbWU=
echo -n "password" | base64  # Output: cGFzc3dvcmQ=
```

Create `mongo-secret.yaml` with encoded values. **Important**: Never commit this file to git (add to `.gitignore`).

Apply secrets:

```sh
kubectl apply -f mongo-secret.yaml
```

**Why first?** MongoDB Deployment references these secrets. Without them, the Deployment will fail to create Pods.

#### 2. Deploy MongoDB

```sh
kubectl apply -f mongo-deployment.yaml
```

This creates:

- **Deployment**: Manages MongoDB Pod replicaset
- **Service** (`mongodb-service`): DNS entry allowing other Pods to connect via `mongodb-service:27017`

Verify:

```sh
kubectl get pods                    # Check MongoDB Pod is Running
kubectl describe service mongodb-service  # View service details
```

#### 3. Create ConfigMap

```sh
kubectl apply -f mongo-config-map.yaml
```

Stores the MongoDB service URL (`mongodb-service`) that Mongo-Express will use to connect. Keep credentials **out of ConfigMaps** - use Secrets instead.

#### 4. Deploy Mongo-Express

```sh
kubectl apply -f mongo-express.yaml
```

This creates:

- **Deployment**: Manages Mongo-Express Pod
- **Service**: Exposes Mongo-Express web UI (port 8081)

The Pod will:

1. Read credentials from Secret (`ME_CONFIG_MONGODB_ADMINUSERNAME`, `ME_CONFIG_MONGODB_ADMINPASSWORD`)
2. Read service URL from ConfigMap (`ME_CONFIG_MONGODB_SERVER`)
3. Connect to MongoDB via DNS: `mongodb-service:27017`

Verify:

```sh
kubectl get pods                    # Check Mongo-Express Pod is Running
kubectl logs deployment/mongo-express  # View connection logs
```

### Complete Deployment Script

```sh
# Deploy in correct order
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-deployment.yaml
kubectl apply -f mongo-config-map.yaml
kubectl apply -f mongo-express.yaml

# Verify all services
kubectl get all
```

### Accessing Mongo-Express

After all Pods are running, expose Mongo-Express:

```sh
# For Minikube
minikube service mongo-express-service
```

### Cleanup

```sh
# Delete all resources
kubectl delete deployment mongo-express
kubectl delete deployment mongodb-deployment
kubectl delete service mongodb-service
kubectl delete configmap mongodb-configmap
kubectl delete secret mongodb-secret

# Or delete by files
kubectl delete -f mongo-express.yaml
kubectl delete -f mongo-deployment.yaml
kubectl delete -f mongo-config-map.yaml
kubectl delete -f mongo-secret.yaml
```

## Resources

- [Minikube Documentation](https://minikube.sigs.k8s.io/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
