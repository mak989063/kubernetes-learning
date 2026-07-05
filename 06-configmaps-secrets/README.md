# Module 06: ConfigMaps & Secrets

## Objective

Learn how Kubernetes manages:

- Application configuration using ConfigMaps
- Sensitive information using Secrets
- Environment variable injection into Pods
- Separation of configuration from application code

---

# Part 1: ConfigMaps

## What is a ConfigMap?

A ConfigMap stores non-sensitive configuration data separately from application code.

Examples:

- Environment names
- Application settings
- API URLs
- Feature flags
- Logging levels

---

## Create a ConfigMap

### configmap.yaml

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: app-config

data:
  ENV: "development"
  APP_NAME: "kubernetes-learning"
  LOG_LEVEL: "debug"
```

---

## Deploy ConfigMap

```bash
kubectl apply -f configmap.yaml
```

---

## Verify ConfigMap

```bash
kubectl get configmaps

kubectl describe configmap app-config
```

Example:

```text
Data
====

APP_NAME:
kubernetes-learning

ENV:
development

LOG_LEVEL:
debug
```

---

## Consume ConfigMap in a Pod

### nginx-configmap-pod.yaml

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-config-demo

spec:
  containers:
    - name: nginx
      image: nginx:latest

      env:
        - name: ENV
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: ENV

        - name: APP_NAME
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_NAME

        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: LOG_LEVEL
```

---

## Deploy the Pod

```bash
kubectl apply -f nginx-configmap-pod.yaml
```

---

## Verify Environment Variables

```bash
kubectl exec -it nginx-config-demo -- env | grep ENV

kubectl exec -it nginx-config-demo -- env | grep APP_NAME

kubectl exec -it nginx-config-demo -- env | grep LOG_LEVEL
```

Expected Output:

```text
ENV=development
APP_NAME=kubernetes-learning
LOG_LEVEL=debug
```

---

## Internal Flow

```text
ConfigMap (app-config)

ENV=development
APP_NAME=kubernetes-learning
LOG_LEVEL=debug

        │
        ▼

Pod Startup

        │
        ▼

Environment Variables

ENV=development
APP_NAME=kubernetes-learning
LOG_LEVEL=debug

        │
        ▼

Application Container
```

---

## Observation

The Pod successfully consumed ConfigMap values as environment variables.

---

## Inference

Applications can use external configuration without modifying container images.

---

# Part 2: Secrets

## What is a Secret?

A Secret stores sensitive information separately from application code.

Examples:

- Database passwords
- API keys
- Access tokens
- Certificates
- SSH keys

---

## ConfigMap vs Secret

| ConfigMap | Secret |
|-----------|----------|
| Non-sensitive data | Sensitive data |
| Plain text | Base64 encoded |
| Configuration | Passwords, Tokens |

---

## Create a Secret

Generate Base64 values:

```bash
echo -n "admin" | base64

echo -n "admin123" | base64
```

Output:

```text
YWRtaW4=
YWRtaW4xMjM=
```

---

### secret.yaml

```yaml
apiVersion: v1
kind: Secret

metadata:
  name: db-secret

type: Opaque

data:
  username: YWRtaW4=
  password: YWRtaW4xMjM=
```

---

## Deploy Secret

```bash
kubectl apply -f secret.yaml
```

---

## Verify Secret

```bash
kubectl get secrets

kubectl describe secret db-secret
```

Example:

```text
Type: Opaque

Data
====

username: 5 bytes
password: 8 bytes
```

---

## Consume Secret in a Pod

### nginx-secret-pod.yaml

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-secret-demo

spec:
  containers:
    - name: nginx
      image: nginx:latest

      env:
        - name: DB_USER
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: username

        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
```

---

## Deploy the Pod

```bash
kubectl apply -f nginx-secret-pod.yaml
```

---

## Verify Environment Variables

```bash
kubectl exec -it nginx-secret-demo -- env | grep DB
```

Expected Output:

```text
DB_USER=admin
DB_PASSWORD=admin123
```

---

## Why Is the Password Visible Inside the Container?

The application needs the actual password to communicate with external systems.

Internal Flow:

```text
Secret (db-secret)

username = admin
password = admin123

        │
        ▼

Pod Startup

        │
        ▼

Environment Variables

DB_USER=admin
DB_PASSWORD=admin123

        │
        ▼

Application Container
```

Anyone with `kubectl exec` permissions can view these variables.

Security is enforced using:

- RBAC
- Least Privilege Access
- External Secret Managers

---

## Important Note

Base64 is NOT encryption.

Example:

```bash
echo YWRtaW4xMjM= | base64 -d
```

Output:

```text
admin123
```

Production systems typically use:

- HashiCorp Vault
- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager

---

## Observation

The Pod successfully consumed Secret values as environment variables.

---

## Inference

Applications can securely receive sensitive information without hardcoding credentials inside container images.

---

# Commands Used

## ConfigMaps

```bash
kubectl apply -f configmap.yaml

kubectl get configmaps

kubectl describe configmap app-config

kubectl apply -f nginx-configmap-pod.yaml

kubectl exec -it nginx-config-demo -- env
```

---

## Secrets

```bash
kubectl apply -f secret.yaml

kubectl get secrets

kubectl describe secret db-secret

kubectl apply -f nginx-secret-pod.yaml

kubectl exec -it nginx-secret-demo -- env
```

---

# Key Takeaways

- ConfigMaps store non-sensitive configuration.
- Secrets store sensitive information.
- Applications consume both as environment variables.
- Base64 is not encryption.
- RBAC protects Secret access.
- External Secret Managers are recommended for production environments.

