# Lab 01 - emptyDir Volume

## Objective

Learn how to use an `emptyDir` volume to share data between containers in the same Pod.

---

## What is an emptyDir Volume?

An `emptyDir` volume is created when a Pod is assigned to a worker node.

- Initially empty.
- Shared by all containers in the Pod.
- Exists as long as the Pod exists.
- Deleted automatically when the Pod is deleted.

It is commonly used for:

- Sharing files between containers
- Sidecar logging
- Temporary storage
- Caching
- Intermediate processing

---

## Architecture

```
                Kubernetes Pod
+------------------------------------------+
|                                          |
|   Container A          Container B       |
|   (Writer)             (Reader)          |
|       |                    ^             |
|       |                    |             |
|       +------ emptyDir -----+            |
|                                          |
+------------------------------------------+
```

---

## How emptyDir Works

1. Pod starts.
2. Kubernetes creates an empty directory on the worker node.
3. Both containers mount the directory.
4. One container writes data.
5. Another container reads the same data.
6. Deleting the Pod deletes the volume.

---

## Advantages

- Very fast
- Easy to configure
- No external storage required
- Ideal for temporary data
- Supports multiple containers

---

## Limitations

- Data is deleted when Pod is deleted.
- Cannot survive Pod recreation.
- Cannot be shared across Pods.

---

## Use Cases

- Sidecar containers
- Adapter pattern
- Cache
- Temporary downloads
- Build artifacts
- Log sharing

---

## Interview Questions

### What is emptyDir?

A temporary volume created when a Pod starts and deleted when the Pod is removed.

---

### Where is emptyDir stored?

On the worker node.

---

### Can multiple containers use emptyDir?

Yes.

---

### Can multiple Pods share emptyDir?

No.

---

### Does emptyDir survive Pod deletion?

No.

---

## Key Takeaways

- Pod-scoped storage
- Shared between containers
- Temporary storage
- Deleted with Pod
