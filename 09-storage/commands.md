# Commands – emptyDir Volume

## 1. Create the Pod

```bash
kubectl apply -f emptydir-pod.yaml
```

---

## 2. Verify Pod Status

```bash
kubectl get pods
```

Expected Output:

```text
NAME            READY   STATUS    RESTARTS   AGE
emptydir-demo   2/2     Running   0          20s
```

---

## 3. Describe the Pod

```bash
kubectl describe pod emptydir-demo
```

Verify:

- Pod is Running
- Two containers are present
- `emptyDir` volume is attached

---

## 4. View the Pod YAML

```bash
kubectl get pod emptydir-demo -o yaml
```

Verify:

```yaml
volumes:
- emptyDir: {}
  name: shared-storage
```

---

## 5. Verify Container Names

```bash
kubectl get pod emptydir-demo -o jsonpath='{.spec.containers[*].name}'
```

Expected Output:

```text
writer reader
```

---

## 6. Verify Volume Mounts

```bash
kubectl get pod emptydir-demo -o jsonpath='{.spec.containers[*].volumeMounts[*].mountPath}'
```

Expected Output:

```text
/shared /shared
```

---

## 7. Read the Shared File from Writer Container

```bash
kubectl exec emptydir-demo -c writer -- cat /shared/time.txt
```

Expected Output:

```text
Fri Jul 18 10:00:05 UTC 2026
Fri Jul 18 10:00:10 UTC 2026
...
```

---

## 8. Read the Same File from Reader Container

```bash
kubectl exec emptydir-demo -c reader -- cat /shared/time.txt
```

Expected Output:

```text
Fri Jul 18 10:00:05 UTC 2026
Fri Jul 18 10:00:10 UTC 2026
...
```

Both containers should display identical contents.

---

## 9. Open an Interactive Shell

Writer container:

```bash
kubectl exec -it emptydir-demo -c writer -- sh
```

Reader container:

```bash
kubectl exec -it emptydir-demo -c reader -- sh
```

Exit:

```bash
exit
```

---

## 10. List Files in the Shared Directory

```bash
kubectl exec emptydir-demo -c writer -- ls -l /shared
```

Expected Output:

```text
total 4
-rw-r--r--    1 root root   xxx Jul 18 10:05 time.txt
```

---

## 11. Watch the File Grow

```bash
kubectl exec emptydir-demo -c reader -- tail -f /shared/time.txt
```

Press **Ctrl+C** to stop.

---

## 12. View Container Logs

Writer container:

```bash
kubectl logs emptydir-demo -c writer
```

Reader container:

```bash
kubectl logs emptydir-demo -c reader
```

---

## 13. Delete the Pod

```bash
kubectl delete pod emptydir-demo
```

---

## 14. Verify Pod Deletion

```bash
kubectl get pods
```

Expected Output:

```text
No resources found.
```

The `emptyDir` volume and all its data are deleted along with the Pod.

---

# Flow Diagram

```
                  Kubernetes Pod
+------------------------------------------------+
|                                                |
|  +-------------+        +-------------+        |
|  |   Writer    |        |   Reader    |        |
|  |             |        |             |        |
|  | Writes      |        | Reads       |        |
|  | time.txt    |        | time.txt    |        |
|  +------+------+\      /+------+------+
|         |              |               |
|         +------ emptyDir Volume -------+
|                (/shared)               |
+------------------------------------------------+
```

---

# Key Takeaways

- `emptyDir` is created when the Pod starts.
- The volume is shared by all containers in the same Pod.
- Data persists while the Pod is running.
- The volume is deleted automatically when the Pod is deleted.
- `emptyDir` is ideal for temporary storage, caching, and inter-container communication.
- `emptyDir` cannot be shared between different Pods.
