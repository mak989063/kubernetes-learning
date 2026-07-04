# Lab 03: ReplicaSets

## Objective

Learn how ReplicaSets provide self-healing and maintain the desired number of Pod replicas using the Kubernetes reconciliation loop.

---

## What is a ReplicaSet?

A ReplicaSet is a Kubernetes controller that ensures a specified number of identical Pod replicas are running at all times.

If a Pod fails or is deleted, the ReplicaSet automatically creates a new Pod to maintain the desired state.

---

## ReplicaSet Architecture

```text
ReplicaSet Controller
        │
        ▼
Desired State: 3 Pods
        │
        ▼
┌─────────────┐
│ Pod-1       │
├─────────────┤
│ Pod-2       │
├─────────────┤
│ Pod-3       │
└─────────────┘
```

---

## ReplicaSet Manifest

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

---

## Understanding the YAML Structure

| Field | Description |
|---------|-------------|
| `apiVersion` | API version for ReplicaSets |
| `kind` | Resource type (ReplicaSet) |
| `metadata` | Name and metadata |
| `replicas` | Desired number of Pods |
| `selector` | Identifies Pods managed by the ReplicaSet |
| `matchLabels` | Label matching criteria |
| `template` | Pod blueprint used to create new Pods |
| `containers` | Container definitions |

---

## Deploy the ReplicaSet

```bash
kubectl apply -f nginx-rs.yaml
```

---

## Verify ReplicaSets

```bash
kubectl get rs
```

Example:

```text
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         3       20s
```

Meaning:

| Column | Description |
|----------|-------------|
| DESIRED | Number of Pods requested |
| CURRENT | Pods currently created |
| READY | Pods in Ready state |
| AGE | ReplicaSet age |

---

## View Managed Pods

```bash
kubectl get pods -o wide
```

Example:

```text
NAME             READY   STATUS
nginx-rs-x1y2z   1/1     Running
nginx-rs-a3b4c   1/1     Running
nginx-rs-d5e6f   1/1     Running
```

---

## Labels and Selectors

ReplicaSets use labels to identify the Pods they manage.

### Pod Labels

```yaml
labels:
  app: nginx
```

### Selector

```yaml
selector:
  matchLabels:
    app: nginx
```

The selector and Pod labels must match.

---

## Desired State vs Actual State

Kubernetes continuously compares:

```text
Desired State: 3 Pods
Actual State: 3 Pods
```

Everything is healthy.

---

If a Pod is deleted:

```text
Desired State: 3 Pods
Actual State: 2 Pods
```

The ReplicaSet Controller detects the difference and creates a new Pod.

---

## Self-Healing Demonstration

Delete one Pod:

```bash
kubectl delete pod <pod-name>
```

Immediately verify:

```bash
kubectl get pods
```

A new Pod appears automatically.

This behavior is called **Self-Healing**.

---

## The Reconciliation Loop

Kubernetes uses a continuous reconciliation loop.

```text
Check Actual State
        │
        ▼
Compare with Desired State
        │
        ▼
Take Corrective Action
        │
        ▼
Repeat Forever
```

This is called **Level-Based Reconciliation**.

Kubernetes cares about the current state rather than individual events.

---

## Scaling ReplicaSets

### Scale Up

```bash
kubectl scale rs nginx-rs --replicas=5
```

---

### Scale Down

```bash
kubectl scale rs nginx-rs --replicas=2
```

---

Verify:

```bash
kubectl get rs
kubectl get pods
```

---

## Describe a ReplicaSet

```bash
kubectl describe rs nginx-rs
```

Important sections:

- Replicas
- Selector
- Pod Template
- Controlled Pods
- Events

---

## ReplicaSet vs Standalone Pod

| Standalone Pod | ReplicaSet |
|---------------|-------------|
| No self-healing | Self-healing |
| Manual scaling | Automatic scaling |
| Single Pod | Multiple Pod replicas |
| No controller | Managed by ReplicaSet Controller |

---

## ReplicaSet vs Deployment

| ReplicaSet | Deployment |
|------------|------------|
| Manages Pods | Manages ReplicaSets |
| No rolling updates | Rolling updates supported |
| No rollback capability | Rollbacks supported |
| Rarely created directly | Preferred in production |

---

## Important Questions to be answered

### What is a ReplicaSet?

A ReplicaSet ensures that a specified number of identical Pod replicas are running at all times.

---

### What is Self-Healing?

If a Pod fails or is deleted, Kubernetes automatically creates a replacement Pod to maintain the desired number of replicas.

---

### What is the Reconciliation Loop?

The reconciliation loop continuously compares the actual state with the desired state and takes corrective actions whenever they differ.

---

### Why are ReplicaSets rarely created directly?

Deployments provide higher-level functionality such as rolling updates, rollbacks, and revision history, making them the preferred choice in production environments.

---

## Key Takeaways

- ReplicaSets provide self-healing.
- ReplicaSets maintain the desired number of Pods.
- Labels and selectors determine Pod ownership.
- Kubernetes uses a reconciliation loop to maintain state.
- ReplicaSets support scaling operations.
- Deployments are preferred over direct ReplicaSet usage in production.


