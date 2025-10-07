---
tags:
  - k8s/scheduler
  - k8s/nodes
  - k8s/workloads
---
Node affinity constrains which nodes a Pod can be scheduled on by matching labels advertised on nodes. It replaces older `nodeSelector` usage with richer matching and priority expressions.

## Scheduling Modes

- `requiredDuringSchedulingIgnoredDuringExecution`: hard requirement; Pods will only land on nodes that satisfy every expression.
- `preferredDuringSchedulingIgnoredDuringExecution`: soft preference; the scheduler scores matching nodes higher but falls back to any schedulable node.
- Expressions use `operator` values such as `In`, `NotIn`, `Exists`, or `DoesNotExist` against node labels (`kubernetes.io/zone`, custom topology labels, etc.).

> [!INFO] Node Labels
> Check node labels with `kubectl get nodes --show-labels` and add custom labels via `kubectl label nodes node-1 gpu=true`.

## Example Manifest

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payments-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: payments
  template:
    metadata:
      labels:
        app: payments
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node.kubernetes.io/instance-type
                operator: In
                values:
                - m6i.large
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 50
            preference:
              matchExpressions:
              - key: topology.kubernetes.io/zone
                operator: In
                values:
                - us-east-1a
                - us-east-1b
      containers:
      - name: api
        image: ghcr.io/company/payments:1.4.2
```

## Operational Snippets

```sh
# Review scheduling decisions for a Pod
kubectl describe pod payments-api-xyz | Select-String -Pattern 'nodeAffinity'

# Add a label to a node and confirm
kubectl label node worker-02 env=prod
kubectl get nodes worker-02 --show-labels
```
