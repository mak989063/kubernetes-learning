# Kubernetes Learning 🚀

Hands-on Kubernetes learning repository using Kind (Kubernetes in Docker) on macOS.

This repository documents my end-to-end Kubernetes journey, covering core concepts, cluster administration, application deployment, networking, storage, Helm, and real-world DevOps practices.

---

## Environment

- MacBook Air M4 (24 GB RAM)
- Docker Desktop
- kubectl v1.34.1
- Kind v0.32.0
- Kubernetes v1.36.1

---

## Local Cluster Architecture

```text
MacBook Air M4
│
├── Docker Desktop
│
└── Kind Cluster (devops-lab)
    ├── Control Plane
    │   ├── kube-apiserver
    │   ├── kube-scheduler
    │   ├── kube-controller-manager
    │   └── etcd
    │
    ├── Worker Node 1
    │   ├── kubelet
    │   └── kube-proxy
    │
    └── Worker Node 2
        ├── kubelet
        └── kube-proxy
```

---

## Learning Modules

### 01. Kind Cluster Setup
- Install Docker, kubectl, and Kind
- Create a multi-node Kubernetes cluster
- Understand control plane components
- Explore namespaces and system pods

### 02. Pods
- Imperative vs Declarative approach
- Pod lifecycle
- kubectl logs
- kubectl exec
- kubectl describe
- Port forwarding

### 03. ReplicaSets
- Desired vs Actual state
- Self-healing
- Reconciliation loops
- Scaling replicas

### 04. Deployments
- Rolling updates
- Rollbacks
- Revision history
- Zero-downtime deployments

### 05. Services
- ClusterIP
- NodePort
- LoadBalancer
- Service discovery

### 06. ConfigMaps & Secrets
- Environment variables
- Configuration management
- Secret handling

### 07. Namespaces
- Resource isolation
- Multi-team environments
- Resource quotas

### 08. Scheduling
- Labels and Selectors
- Node Selectors
- Taints and Tolerations
- Affinity and Anti-Affinity
- Custom Schedulers

### 09. Storage
- Persistent Volumes (PV)
- Persistent Volume Claims (PVC)
- Storage Classes
- Dynamic Provisioning

### 10. Ingress
- NGINX Ingress Controller
- Path-based Routing
- Host-based Routing
- TLS Termination

### 11. Helm
- Helm Charts
- Values Files
- Releases
- Upgrades and Rollbacks

### 12. Monitoring
- Prometheus
- Grafana
- Metrics Server
- Logging

---

## Projects

Planned hands-on projects:

- Nginx Application Deployment
- Flask Application on Kubernetes
- Three-Tier Application
- CI/CD with Kubernetes
- Monitoring Stack Deployment

---

## Useful Commands

Create Cluster:

```bash
kind create cluster \
  --name devops-lab \
  --config 01-kind-setup/kind-cluster.yaml
```

Check Nodes:

```bash
kubectl get nodes
```

Check System Pods:

```bash
kubectl get pods -A
```

Delete Cluster:

```bash
kind delete cluster --name devops-lab
```

---

## Learning Goal

Build production-ready Kubernetes skills through hands-on practice and real-world DevOps scenarios without relying on managed cloud services such as EKS, AKS, or GKE.

All examples are executed locally using Kind and Docker Desktop.
