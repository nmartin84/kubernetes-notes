---
tags:
  - k8s/scheduler
  - k8s/topology
  - k8s/workloads
---
Pod affinity schedules Pods close to other Pods (same zone, same host, same rack), while pod anti-affinity keeps replicas apart to improve availability. Both rely on matching labels on existing Pods using the target cluster topology.

## Pod Affinity vs. Anti-Affinity

- Affinity (`podAffinity`) co-locates Pods with other Pods that match a `labelSelector` within a `topologyKey` (hostname, zone, region, etc.).
- Anti-affinity (`podAntiAffinity`) spreads Pods across topology domains by avoiding nodes that already run matching Pods.
- Each can be `requiredDuringSchedulingIgnoredDuringExecution` (hard) or `preferredDuringSchedulingIgnoredDuringExecution` (soft).
- Defaults use `topologyKey: kubernetes.io/hostname`; specify wider keys (zones or regions) for failure-domain awareness.

> [!WARNING] Label Cardinality Matters
> Anti-affinity with a common `topologyKey` like `kubernetes.io/hostname` can slow scheduling when thousands of Pods exist; consider topology spread constraints for large clusters.

## Example Manifest

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 4
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
        tier: web
    spec:
      affinity:
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: tier
                operator: In
                values:
                - api
            topologyKey: topology.kubernetes.io/zone
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchLabels:
                  app: frontend
              topologyKey: kubernetes.io/hostname
      containers:
      - name: nginx
        image: nginx:1.27
```

## Operational Snippets

```sh
# Explain why a Pod is pending due to affinity rules
kubectl describe pod frontend-xyz | Select-String -Pattern 'affinity'

# Dry-run an affinity change on a Deployment
kubectl patch deployment frontend --type strategic --patch-file add-anti-affinity.yaml --dry-run=server
```
