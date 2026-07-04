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

## Key Learnings

- Declarative Pod creation
- Pod scheduling
- Pod inspection
- Logs and debugging
- Port forwarding
- Pod lifecycle
- Why Pods are not self-healing 
- A Pod by itself has no controller. If you delete it, it is gone forever. This leads to Module 03: We will discuss there.
