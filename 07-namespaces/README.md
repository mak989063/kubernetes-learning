# Module 07: Namespaces

## Objective

Learn how Kubernetes Namespaces provide:

- Logical isolation of resources
- Environment separation (Dev, Test, Prod)
- Multi-team support
- Better resource organization

---

# What is a Namespace?

A Namespace is a virtual cluster inside a Kubernetes cluster.

It allows multiple teams or environments to share the same cluster without conflicts.

Example:

```text
Kubernetes Cluster

├── dev
│   └── nginx
│
├── prod
│   └── nginx
│
└── monitoring
    └── prometheus
```

Resources with the same name can exist in different namespaces.

Example:

```text
dev/nginx
prod/nginx
```

Both are valid and independent.

---

# Why Do We Need Namespaces?

Without Namespaces:

```text
Cluster

nginx
nginx
nginx

❌ Resource name conflicts
❌ No environment separation
❌ Difficult management
```

With Namespaces:

```text
Cluster

dev/nginx
test/nginx
prod/nginx

✅ Isolation
✅ Better organization
✅ Multi-team support
```

---

# Default Namespaces

Check existing namespaces:

```bash
kubectl get ns
```

Example:

```text
NAME                 STATUS
default              Active
kube-system          Active
kube-public          Active
kube-node-lease      Active
local-path-storage   Active
```

---

## default

Default namespace for user applications.

Example:

```bash
kubectl get pods
```

Without specifying a namespace, Kubernetes uses the default namespace.

---

## kube-system

Contains Kubernetes system components.

Examples:

- API Server
- Scheduler
- Controller Manager
- CoreDNS
- kube-proxy

Check:

```bash
kubectl get pods -n kube-system
```

---

## kube-public

Contains publicly readable resources.

Mostly used internally by Kubernetes.

---

## kube-node-lease

Stores node heartbeat information.

Helps Kubernetes detect node failures quickly.

---

## local-path-storage

Used by Kind for dynamic local volume provisioning.

---

# Create a Development Namespace

## dev.yaml

```yaml
apiVersion: v1
kind: Namespace

metadata:
  name: dev
```

---

## Deploy

```bash
kubectl apply -f dev.yaml
```

Expected Output:

```text
namespace/dev created
```

---

# Create a Production Namespace

## prod.yaml

```yaml
apiVersion: v1
kind: Namespace

metadata:
  name: prod
```

---

## Deploy

```bash
kubectl apply -f prod.yaml
```

Expected Output:

```text
namespace/prod created
```

---

# Verify Namespaces

```bash
kubectl get ns
```

Example:

```text
NAME                 STATUS
default              Active
dev                  Active
prod                 Active
kube-system          Active
```

---

# Deploy Resources into Namespaces

Create an Nginx Pod in the development namespace:

```bash
kubectl run nginx \
  --image=nginx \
  -n dev
```

---

Check:

```bash
kubectl get pods -n dev
```

Expected Output:

```text
NAME    READY   STATUS
nginx   1/1     Running
```

---

Create another Nginx Pod in production:

```bash
kubectl run nginx \
  --image=nginx \
  -n prod
```

---

Check:

```bash
kubectl get pods -n prod
```

Expected Output:

```text
NAME    READY   STATUS
nginx   1/1     Running
```

---

# Namespace Isolation

Both namespaces contain a Pod named:

```text
nginx
```

Internally:

```text
dev/nginx
prod/nginx
```

There is no conflict because Namespaces provide logical isolation.

---

# View Resources Across All Namespaces

Current namespace only:

```bash
kubectl get pods
```

---

All namespaces:

```bash
kubectl get pods -A
```

Example:

```text
NAMESPACE            NAME
default              nginx-config-demo
default              nginx-secret-demo
dev                  nginx
prod                 nginx
kube-system          coredns
```

---

# Switch Default Namespace

Instead of specifying:

```bash
kubectl get pods -n dev
```

You can change the current context:

```bash
kubectl config set-context --current --namespace=dev
```

---

Verify:

```bash
kubectl config view --minify | grep namespace
```

Example:

```text
namespace: dev
```

---

Now:

```bash
kubectl get pods
```

will automatically show resources from the dev namespace.

---

Return to the default namespace:

```bash
kubectl config set-context --current --namespace=default
```

---

# Namespaces in Real Projects

Example:

```text
Production Cluster

├── frontend-team
├── backend-team
├── monitoring
├── logging
├── dev
├── test
└── prod
```

Benefits:

- Team isolation
- Environment separation
- Easier management
- Better security with RBAC

---

# Commands Used

View namespaces:

```bash
kubectl get ns
```

---

Create namespaces:

```bash
kubectl apply -f dev.yaml

kubectl apply -f prod.yaml
```

---

Run pods in namespaces:

```bash
kubectl run nginx --image=nginx -n dev

kubectl run nginx --image=nginx -n prod
```

---

View pods:

```bash
kubectl get pods -n dev

kubectl get pods -n prod

kubectl get pods -A
```

---

Switch namespace:

```bash
kubectl config set-context --current --namespace=dev

kubectl config set-context --current --namespace=default
```

---

# Important Interview Questions

## What is a Namespace?

A Namespace is a logical partition inside a Kubernetes cluster that provides resource isolation and organization.

---

## Why do we need Namespaces?

Namespaces help with:

- Environment separation
- Multi-team support
- Resource isolation
- Better management

---

## Can two resources have the same name in different namespaces?

Yes.

Example:

```text
dev/nginx
prod/nginx
```

Both can exist simultaneously.

---

## What is the default namespace?

The `default` namespace is used when no namespace is explicitly specified.

---

## What resources are present in kube-system?

Examples:

- kube-apiserver
- kube-scheduler
- kube-controller-manager
- coredns
- kube-proxy

---

# Key Takeaways

- Namespaces provide logical isolation within a cluster.
- Multiple environments can share the same Kubernetes cluster.
- Resources with identical names can exist in different namespaces.
- The `default` namespace is used automatically if none is specified.
- `kube-system` contains Kubernetes control plane components.
- Namespaces work together with RBAC to provide security and multi-tenancy.

