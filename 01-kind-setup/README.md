# Lab 01 - Kind Cluster Setup

## Environment

- macOS (Apple Silicon M4)
- Docker Desktop
- kubectl v1.34.1
- kind v0.32.0

## Create Cluster

```bash
kind create cluster \
  --name devops-lab \
  --config 01-kind-setup/kind-cluster.yaml
