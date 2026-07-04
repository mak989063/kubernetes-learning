# Lab 01: Setting Up a Local Kubernetes Cluster with Kind

## Objective

Set up a local multi-node Kubernetes cluster using Kind (Kubernetes IN Docker) on macOS.

---

## Prerequisites

Verify that Docker, kubectl, and Kind are installed.

### Check Docker

```bash
docker version
```

Expected:

- Docker Client information
- Docker Server information
- Docker Desktop running

---

### Check kubectl

```bash
kubectl version --client
```

Expected:

```text
Client Version: v1.34.1
```

---

### Check Kind

```bash
kind version
```

Expected:

```text
kind v0.32.0
```

---

## Cluster Configuration

File: `01-kind-setup/kind-cluster.yaml`

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

nodes:
- role: control-plane
- role: worker
- role: worker
```

---

## Create the Cluster

```bash
kind create cluster \
  --name devops-lab \
  --config 01-kind-setup/kind-cluster.yaml
```

Expected:

```text
✓ Ensuring node image
✓ Preparing nodes
✓ Starting control-plane
✓ Installing CNI
✓ Installing StorageClass
✓ Joining worker nodes
```

---

## Verify the Cluster

### Check Nodes

```bash
kubectl get nodes
```

Example:

```text
NAME                       STATUS   ROLES           VERSION
devops-lab-control-plane   Ready    control-plane   v1.36.1
devops-lab-worker          Ready    <none>          v1.36.1
devops-lab-worker2         Ready    <none>          v1.36.1
```

---

### Check Cluster Information

```bash
kubectl cluster-info
```

Example:

```text
Kubernetes control plane is running at https://127.0.0.1:50470
CoreDNS is running at ...
```

---

### Check Current Context

```bash
kubectl config current-context
```

Expected:

```text
kind-devops-lab
```

---

### List Namespaces

```bash
kubectl get ns
```

Expected:

```text
default
kube-node-lease
kube-public
kube-system
local-path-storage
```

---

### View System Pods

```bash
kubectl get pods -A
```

Important components:

- kube-apiserver
- kube-scheduler
- kube-controller-manager
- etcd
- coredns
- kube-proxy
- kindnet
- local-path-provisioner

---

### View System Pods with Node Details

```bash
kubectl get pods -A -o wide
```

This helps understand:

- Which node a Pod runs on
- Pod IP addresses
- Cluster networking

---

### Inspect a Worker Node

```bash
kubectl describe node devops-lab-worker
```

Important sections:

- Capacity
- Allocatable Resources
- Labels
- Taints
- Conditions
- Running Pods

---

### View Kubernetes Nodes as Docker Containers

```bash
docker ps
```

Example:

```text
devops-lab-control-plane
devops-lab-worker
devops-lab-worker2
```

Kind runs Kubernetes nodes as Docker containers.

---

## Delete the Cluster

```bash
kind delete cluster --name devops-lab
```

---

## Key Learnings

- Installed and verified Docker, kubectl, and Kind
- Created a 3-node Kubernetes cluster
- Explored control-plane components
- Explored worker-node components
- Understood namespaces and system Pods
- Verified Kubernetes networking and storage components
- Learned that Kind runs Kubernetes nodes as Docker containers
