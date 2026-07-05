# Lab 05: Services

## Objective

Learn how Kubernetes Services provide:

- Stable networking for Pods
- Internal service discovery
- Load balancing
- External access to applications

---

## What Problem Do Services Solve?

Pods are ephemeral.

When a Pod is deleted or recreated:

- Pod name changes
- Pod IP address changes

Example:

```text
Old Pod:
nginx-pod
IP: 10.244.1.2

↓

Pod crashes

↓

New Pod:
nginx-pod-new
IP: 10.244.2.5
```

Applications cannot depend on Pod IP addresses.

Services provide:

- Stable Virtual IP (ClusterIP)
- Stable DNS Name
- Automatic Load Balancing

---

## Service Architecture

```text
Application
     │
     ▼
Service (Stable IP)
     │
     ▼
┌──────────────┐
│ Pod 1        │
│ 10.244.1.2   │
├──────────────┤
│ Pod 2        │
│ 10.244.2.2   │
└──────────────┘
```

The Service automatically distributes traffic among all matching Pods.

---

## Labels and Selectors

Pods:

```bash
kubectl get pods --show-labels
```

Example:

```text
NAME                                LABELS
nginx-deployment-xxx               app=nginx
nginx-deployment-yyy               app=nginx
```

Service Selector:

```yaml
selector:
  app: nginx
```

Kubernetes automatically finds all Pods matching this label and adds them as Service Endpoints.

---

# ClusterIP Service

## What is ClusterIP?

ClusterIP is the default Service type.

It exposes applications only inside the Kubernetes cluster.

Used for:

- Microservice communication
- Backend APIs
- Internal applications

---

## clusterip.yaml

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-clusterip

spec:
  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80

  type: ClusterIP
```

---

## Create the Service

```bash
kubectl apply -f clusterip.yaml
```

---

## Verify Services

```bash
kubectl get svc
```

Example:

```text
NAME              TYPE        CLUSTER-IP      PORT(S)
kubernetes        ClusterIP   10.96.0.1       443/TCP
nginx-clusterip   ClusterIP   10.96.101.206   80/TCP
```

---

## Describe the Service

```bash
kubectl describe svc nginx-clusterip
```

Example:

```text
Selector:
app=nginx

Endpoints:
10.244.1.2:80
10.244.2.2:80
```

---

## Understanding Endpoints

Endpoints are the actual backend Pods serving traffic.

```text
ClusterIP Service
10.96.101.206
       │
       ▼
┌─────────────┐
│10.244.1.2:80│
├─────────────┤
│10.244.2.2:80│
└─────────────┘
```

---

## EndpointSlices

Modern Kubernetes uses EndpointSlices instead of Endpoints.

Check:

```bash
kubectl get endpointslices
```

Example:

```text
NAME                    ADDRESSTYPE   PORTS   ENDPOINTS
nginx-clusterip-7mbt5   IPv4          80      10.244.1.2,10.244.2.2
```

Advantages:

- Better scalability
- Better performance
- Supports large clusters

---

# NodePort Service

## What is NodePort?

NodePort exposes applications outside the cluster.

Port Range:

```text
30000–32767
```

Traffic Flow:

```text
Browser
   │
   ▼
NodeIP:30080
   │
   ▼
NodePort Service
   │
   ▼
Pods
```

---

## nodeport.yaml

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-nodeport

spec:
  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080

  type: NodePort
```

---

## Create the Service

```bash
kubectl apply -f nodeport.yaml
```

---

## Verify NodePort Service

```bash
kubectl get svc
```

Example:

```text
NAME             TYPE       CLUSTER-IP      PORT(S)
nginx-nodeport   NodePort   10.96.1.149     80:30080/TCP
```

---

## Describe the NodePort Service

```bash
kubectl describe svc nginx-nodeport
```

Example:

```text
Type: NodePort

NodePort:
30080/TCP

Endpoints:
10.244.1.2:80
10.244.2.2:80
```

---

## Important Concept

Every NodePort Service automatically creates a ClusterIP Service.

Architecture:

```text
Browser
   │
   ▼
NodePort (30080)
   │
   ▼
ClusterIP (10.96.1.149)
   │
   ▼
Pods
```

NodePort builds on top of ClusterIP.

---

# Service Types

## ClusterIP (Default)

```text
Access:
Inside Cluster Only
```

Used for:

- Internal APIs
- Backend Services
- Microservices

---

## NodePort

```text
Access:
Outside Cluster
```

Used for:

- Development
- Testing
- Small deployments

---

## LoadBalancer

Used in Cloud Platforms:

- AWS ELB
- Azure Load Balancer
- Google Cloud Load Balancer

Example:

```yaml
type: LoadBalancer
```

Not applicable for local Kind clusters.

---

## ExternalName

Maps a Service to an external DNS name.

Example:

```yaml
type: ExternalName
externalName: google.com
```

---

# CoreDNS

CoreDNS provides DNS inside Kubernetes.

Example:

```bash
curl http://nginx-clusterip
```

Kubernetes resolves:

```text
nginx-clusterip.default.svc.cluster.local
```

Check:

```bash
kubectl get pods -n kube-system | grep coredns
```

---

# kube-proxy

kube-proxy runs on every node.

Responsibilities:

- Service networking
- Load balancing
- Packet forwarding
- Managing iptables/ipvs rules

Check:

```bash
kubectl get pods -n kube-system | grep kube-proxy
```

---

# ClusterIP vs NodePort

| Feature | ClusterIP | NodePort |
|----------|------------|-----------|
| Default Type | Yes | No |
| External Access | No | Yes |
| Internal Access | Yes | Yes |
| Uses Fixed Ports | No | Yes |
| Typical Usage | Microservices | Development & Testing |

---

# Important Interview Questions

## Why do we need Services?

Pods are ephemeral and their IP addresses change.

Services provide:

- Stable IP addresses
- DNS names
- Load balancing

---

## What is ClusterIP?

ClusterIP is the default Service type that exposes applications only inside the Kubernetes cluster.

---

## What is NodePort?

NodePort exposes applications externally using ports between 30000–32767.

---

## What is an EndpointSlice?

EndpointSlices are scalable replacements for the older Endpoints resource.

They store backend Pod IP addresses used by Services.

---

## What is CoreDNS?

CoreDNS provides DNS-based service discovery inside Kubernetes.

---

## What is kube-proxy?

kube-proxy manages Service networking and load balancing on every Kubernetes node.

---

# Commands Used in This Lab

Create ClusterIP Service:

```bash
kubectl apply -f clusterip.yaml
```

Create NodePort Service:

```bash
kubectl apply -f nodeport.yaml
```

View Services:

```bash
kubectl get svc
```

Describe Service:

```bash
kubectl describe svc nginx-clusterip

kubectl describe svc nginx-nodeport
```

View EndpointSlices:

```bash
kubectl get endpointslices
```

View Endpoints (Deprecated):

```bash
kubectl get endpoints
```

Check CoreDNS:

```bash
kubectl get pods -n kube-system | grep coredns
```

Check kube-proxy:

```bash
kubectl get pods -n kube-system | grep kube-proxy
```

---

# Key Takeaways

- Services provide stable networking for Pods.
- ClusterIP is used for internal communication.
- NodePort exposes applications outside the cluster.
- Services use labels and selectors to find Pods.
- EndpointSlices store backend Pod information.
- CoreDNS provides service discovery.
- kube-proxy manages Service networking and load balancing.

