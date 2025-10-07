---
tags:
  - k8s/namespace
  - k8s/management
---
In Kubernetes, a **Namespace** is a logical partition of the cluster — like a “virtual cluster” inside the physical cluster.

It helps organize, isolate, and manage resources (Pods, Services, Deployments, ConfigMaps, etc.) for different teams, environments, or applications.

## Manifest File

```yaml
apiVersion: v1:
kind: Namespace
metadata:
  name: team2
```

## Managing Namespaces

```sh
kubectl create ns team1
kubectl get ns
```

## Limiting Resources in Namespace

CPU and Memory Limits:
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: mem-cpu-limits
spec:
  hard:
    requests.cpu: "1"
    requests.memory: "1Gi"
    limits.cpu: "2"
    limits.memory: "2Gi"
```

Pod  Limits
```yaml
apiversion: v1
kind: ResourceQuota
metadata:
  name: pod-limits
spec:
  hard:
    pods: "2"
```

## Applying Resource to Namespace

When you deploy you'll just add a  flag to specify the namespace:

```sh
kubectl apply -f busybox-pod1 --namespace=team1
kubectl apply -f mem-limits.yaml --namespace=team1
kubectl apply -f pod-limits.yaml --namespace=team1
```
