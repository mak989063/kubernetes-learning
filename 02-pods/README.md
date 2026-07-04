# Lab 02: Pods

## Create Pod

```bash
kubectl apply -f nginx-pod.yaml
```

## List Pods

```bash
kubectl get pods
kubectl get pods -o wide
```

## Describe Pod

```bash
kubectl describe pod nginx-pod
```

## View Logs

```bash
kubectl logs nginx-pod
```

## Execute Inside Pod

```bash
kubectl exec -it nginx-pod -- sh
```

## Port Forward

```bash
kubectl port-forward pod/nginx-pod 8080:80
```

Open:

http://localhost:8080

## Delete Pod

```bash
kubectl delete pod nginx-pod
```

## Pod Lifecycle

```text
Pending
   ↓
ContainerCreating
   ↓
Running
   ↓
Succeeded / Failed
```

### Pod States

- **Pending**: Pod accepted by Kubernetes but not yet running.
- **Running**: At least one container is running.
- **Succeeded**: All containers completed successfully.
- **Failed**: One or more containers terminated with failure.
- **Unknown**: Kubernetes cannot determine the Pod state, usually due to node communication issues.

---

## Understanding the YAML Structure

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx:latest
```

| Field | Description |
|--------|-------------|
| `apiVersion` | Kubernetes API version |
| `kind` | Resource type (Pod) |
| `metadata` | Name, labels, and annotations |
| `spec` | Desired state definition |
| `containers` | List of containers inside the Pod |

---

## Imperative vs Declarative Approach

### Imperative Approach

```bash
kubectl run nginx-pod --image=nginx
```
Quick and useful for testing and learning.

### Declarative Approach

```bash
kubectl apply -f nginx-pod.yaml
```
Recommended for production because everything is defined in code, version-controlled, and reproducible.

## Key Learning Points

- Declarative Pod creation
- Pod scheduling
- Pod inspection
- Logs and debugging
- Port forwarding
- Pod lifecycle
- Why Pods are not self-healing?

## Key Takeaways

- A Pod is the smallest deployable unit in Kubernetes.
- Pods can be created using imperative (`kubectl run`) or declarative (YAML) approaches.
- `kubectl describe`, `logs`, `exec`, and `port-forward` are essential debugging commands.
- The Kubernetes Scheduler decides which node runs a Pod.
- A standalone Pod is **not self-healing**.

> **Important:**  
> A standalone Pod is not self-healing. If you delete it, Kubernetes does not recreate it because no controller manages its desired state. Self-healing is introduced by higher-level controllers such as ReplicaSets and Deployments.
