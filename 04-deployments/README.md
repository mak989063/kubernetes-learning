# Lab 04: Deployments

## Objective

Learn how Deployments manage ReplicaSets and Pods to provide:

- Self-healing
- Rolling Updates
- Rollbacks
- Revision History
- Zero-Downtime Deployments
- Scaling Applications

---

## What is a Deployment?

A Deployment is a higher-level Kubernetes controller that manages ReplicaSets.

A Deployment ensures that the desired application state is maintained and provides advanced features such as rolling updates and rollbacks.

In production environments, Deployments are the preferred way to run stateless applications.

---

## Deployment Architecture

```text
Deployment
    │
    ▼
ReplicaSet
    │
    ▼
Pods
```

The hierarchy is:

- Deployment manages ReplicaSets
- ReplicaSets manage Pods
- Pods run Containers

---

## Why Use Deployments?

Deployments provide:

- Self-healing
- Automatic scaling
- Rolling updates
- Rollbacks
- Revision history
- Zero-downtime application upgrades

---

## Deployment Manifest

File: `nginx-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment
  labels:
    app: nginx

spec:
  replicas: 2

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
          image: nginx:1.27
          ports:
            - containerPort: 80
```

---

## Understanding the YAML Structure

| Field | Description |
|--------|-------------|
| `apiVersion` | API version for Deployments |
| `kind` | Resource type (Deployment) |
| `metadata` | Name, labels, annotations |
| `replicas` | Desired number of Pod replicas |
| `selector` | Identifies Pods managed by the Deployment |
| `template` | Blueprint used to create Pods |
| `containers` | Container definitions |

---

## Create the Deployment

```bash
kubectl apply -f nginx-deployment.yaml
```

Expected:

```text
deployment.apps/nginx-deployment created
```

---

## View Deployments

```bash
kubectl get deployments
```

Example:

```text
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           5m
```

---

## Understanding Deployment Columns

| Column | Description |
|----------|-------------|
| READY | Running and healthy Pods |
| UP-TO-DATE | Pods using the latest specification |
| AVAILABLE | Pods available to serve traffic |
| AGE | Deployment age |

---

## View ReplicaSets

```bash
kubectl get rs
```

Example:

```text
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-775786f995   2         2         2       5m
```

The Deployment automatically creates a ReplicaSet.

---

## View Pods

```bash
kubectl get pods
```

Example:

```text
NAME                                READY   STATUS
nginx-deployment-775786f995-abcde   1/1     Running
nginx-deployment-775786f995-fghij   1/1     Running
```

---

## Describe the Deployment

```bash
kubectl describe deployment nginx-deployment
```

Important sections:

- Labels
- Replicas
- Strategy Type
- Pod Template
- Conditions
- Events

---

## Scaling a Deployment

### Scale Up

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Verify:

```bash
kubectl get deployments
kubectl get pods
```

---

### Scale Down

```bash
kubectl scale deployment nginx-deployment --replicas=2
```

---

## Rolling Updates

One of the most powerful features of Deployments is Rolling Updates.

---

### Update the Container Image

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.28
```

Example:

```text
deployment.apps/nginx-deployment image updated
```

---

## What Happens Internally?

Before the update:

```text
Deployment
    │
    ▼
ReplicaSet (775786f995)
    │
    ├── Pod 1 (nginx:1.27)
    └── Pod 2 (nginx:1.27)
```

After the image update:

```text
Deployment
│
├── Old ReplicaSet (775786f995)
│       └── Pod (nginx:1.27)
│
└── New ReplicaSet (859b74487b)
        ├── Pod (nginx:1.28)
        └── Pod (nginx:1.28)
```

---

## Why Was a New ReplicaSet Created?

Changing the image changes the Pod Template.

Old:

```yaml
image: nginx:1.27
```

New:

```yaml
image: nginx:1.28
```

A changed Pod template means:

```text
New Template
    ↓
New Hash
    ↓
New ReplicaSet
```

---

## Understanding ReplicaSet Hashes

Example:

```text
nginx-deployment-775786f995
nginx-deployment-859b74487b
```

These numbers are generated from the Pod template hash.

Different templates produce different ReplicaSets.

---

## Checking ReplicaSets During Rolling Update

```bash
kubectl get rs
```

Example:

```text
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-775786f995   1         1         1       8m
nginx-deployment-859b74487b   2         2         2       30s
```

---

## How Rolling Updates Work

Instead of:

```text
Delete all old Pods
Create all new Pods
```

which causes downtime,

Kubernetes performs:

```text
Old:
Pod-1 (1.27)
Pod-2 (1.27)

↓

Create:
Pod-3 (1.28)

↓

Wait until Ready

↓

Delete:
Pod-1 (1.27)

↓

Create:
Pod-4 (1.28)

↓

Delete:
Pod-2 (1.27)
```

This ensures:

- High Availability
- Zero Downtime
- Safe Application Upgrades

---

## Check Rollout Status

```bash
kubectl rollout status deployment/nginx-deployment
```

Example:

```text
deployment "nginx-deployment" successfully rolled out
```

---

## Revision History

Deployments maintain revision history.

Check:

```bash
kubectl rollout history deployment/nginx-deployment
```

Example:

```text
deployment.apps/nginx-deployment

REVISION
1
2
```

---

## Rollbacks

Rollback to the previous version:

```bash
kubectl rollout undo deployment/nginx-deployment
```

Example:

```text
deployment.apps/nginx-deployment rolled back
```

---

## What Happens During Rollback?

Current:

```text
ReplicaSet (nginx:1.28)
```

Rollback:

```text
Deployment
    ↓
Previous ReplicaSet (nginx:1.27)
```

Again, no downtime occurs.

---

## Deployment Strategies

Kubernetes supports multiple deployment strategies.

### RollingUpdate (Default)

```text
Old Pods
    ↓
Gradually Replaced
    ↓
New Pods
```

Advantages:

- Zero downtime
- Safe updates
- Easy rollbacks

---

### Recreate

```text
Delete Old Pods
    ↓
Create New Pods
```

Advantages:

- Simpler

Disadvantages:

- Causes downtime

---

## Deployment vs ReplicaSet

| ReplicaSet | Deployment |
|------------|-------------|
| Self-healing | Self-healing |
| Scaling | Scaling |
| No rollbacks | Rollbacks |
| No rolling updates | Rolling updates |
| No revision history | Revision history |
| Rarely created directly | Preferred in production |

---

## Deployment vs Standalone Pod

| Pod | Deployment |
|------|------------|
| No self-healing | Self-healing |
| Single instance | Multiple replicas |
| No updates | Rolling updates |
| No rollbacks | Rollbacks |
| Manual scaling | Automatic scaling |

---

## Important Interview Questions

### What is a Deployment?

A Deployment is a Kubernetes controller that manages ReplicaSets and provides rolling updates, rollbacks, scaling, and revision history.

---

### Why do Deployments create ReplicaSets?

Deployments use ReplicaSets to maintain application versions and support rolling updates and rollbacks.

---

### Why was a new ReplicaSet created after updating the image?

Changing the Pod template generates a new hash, which creates a new ReplicaSet.

---

### What is a Rolling Update?

A rolling update gradually replaces old Pods with new Pods without causing downtime.

---

### What is a Rollback?

A rollback restores the application to a previous Deployment revision.

---

### Why are Deployments preferred in production?

Deployments provide:

- Self-healing
- Scaling
- Rolling updates
- Rollbacks
- Revision history
- Zero-downtime deployments

---

## Commands Used in This Lab

Create Deployment:

```bash
kubectl apply -f nginx-deployment.yaml
```

View Deployments:

```bash
kubectl get deployments
```

View ReplicaSets:

```bash
kubectl get rs
```

View Pods:

```bash
kubectl get pods
```

Describe Deployment:

```bash
kubectl describe deployment nginx-deployment
```

Scale Deployment:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Update Image:

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.28
```

Check Rollout Status:

```bash
kubectl rollout status deployment/nginx-deployment
```

View Revision History:

```bash
kubectl rollout history deployment/nginx-deployment
```

Rollback:

```bash
kubectl rollout undo deployment/nginx-deployment
```

---

## Key Takeaways

- Deployments manage ReplicaSets.
- ReplicaSets manage Pods.
- Deployments support rolling updates.
- Deployments support rollbacks.
- Deployments maintain revision history.
- Deployments provide zero-downtime upgrades.
- Deployments are the preferred way to run applications in Kubernetes.









