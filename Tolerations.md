---
tags:
  - k8s/scheduler
  - k8s/nodes
  - k8s/pods
---
A toleration lets a Pod be scheduled onto a node that carries a matching taint. Without a toleration, the scheduler refuses to place the Pod on that node even if resources are available.

## Key Ideas

- Taints mark nodes as off-limits unless a Pod explicitly tolerates them.
- Matching happens on `key`, `value`, and `effect` (`NoSchedule`, `PreferNoSchedule`, or `NoExecute`).
- Tolerations do not force scheduling; they only remove the taint as a blocker.
- `tolerationSeconds` (only with `NoExecute`) lets a Pod stay on a tainted node for a limited time.

> [!TIP] Pair with Taints
> Use taints to protect special hardware nodes, system-critical nodes, or quarantined nodes, then grant workload access via tolerations.

## Example Manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: log-forwarder
spec:
  tolerations:
  - key: dedicated
    operator: Equal
    value: logging
    effect: NoSchedule
  containers:
  - name: fluentd
    image: ghcr.io/fluent/fluentd:v1
```

## Operational Snippets

```sh
# Inspect taints on nodes
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints

# Add or remove tolerations via patches
kubectl patch deployment web --type merge --patch '{"spec":{"template":{"spec":{"tolerations":[{"key":"dedicated","operator":"Equal","value":"web","effect":"NoSchedule"}]}}}}'
```
