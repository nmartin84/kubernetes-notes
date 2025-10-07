---
tags:
  - k8s/scheduler
  - k8s/workloads
  - k8s/resilience
---
A PriorityClass assigns an integer priority to Pods, letting the scheduler decide which Pods to run first and which Pods to preempt when the cluster is under resource pressure.

## Why Priority Matters

- Higher priority Pods preempt lower priority Pods if resources are scarce (unless `preemptionPolicy: Never`).
- Critical system components typically use predefined classes (`system-cluster-critical`, `system-node-critical`).
- You can mark one class as `globalDefault: true` so Pods without an explicit class inherit it.
- Combine with PodDisruptionBudgets to keep key workloads available even if they preempt others.

> [!TIP] Use Sparingly
> Too many custom priorities complicate troubleshooting; stick to tiers (critical, baseline, batch) rather than per-team values.

## Example Manifest

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: critical-apps
value: 100000
preemptionPolicy: PreemptLowerPriority
globalDefault: false
description: "Keep payment processing online during node pressure."
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payments-api
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: payments
    spec:
      priorityClassName: critical-apps
      containers:
      - name: api
        image: ghcr.io/company/payments:1.4.2
```

## Operational Snippets

```sh
# View available priority classes
kubectl get priorityclasses

# Inspect Pods ordered by priority
kubectl get pods -A -o custom-columns=NS:.metadata.namespace,NAME:.metadata.name,PRIORITY:.spec.priorityClassName
```
