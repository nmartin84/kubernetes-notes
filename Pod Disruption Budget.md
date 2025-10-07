---
tags:
  - k8s/resilience
  - k8s/workloads
  - k8s/operations
---
A Pod Disruption Budget (PDB) limits how many Pods of a replicated workload can be voluntarily disrupted at once, preserving availability during maintenance tasks such as node drain, cluster upgrades, or controller rollouts.

## Key Behaviors

- Applies to voluntary disruptions only (drain, delete, upgrade, autoscaler evictions). Forced failures (node crash) ignore the budget.
- Define either `minAvailable` (absolute or percentage) or `maxUnavailable` to guard the running replica count.
- Matches Pods by label selector, so keep it in sync with Deployment/StatefulSet labels.
- Evictions denied by the API server show `Cannot evict pod as it would violate the pod's disruption budget`.

> [!IMPORTANT] PDBs Need Redundancy
> Ensure the controller has enough replicas; a PDB does not create Pods, it merely blocks evictions.

## Example Manifest

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: frontend-pdb
spec:
  minAvailable: 70%
  selector:
    matchLabels:
      app: frontend
```

## Operational Snippets

```sh
# Describe disruption status
kubectl describe pdb frontend-pdb

# Attempt a drain and watch evictions respect the budget
kubectl drain node/ip-10-0-12-5 --ignore-daemonsets --delete-emptydir-data

# List Pods denied for eviction
kubectl get events --field-selector reason=FailedDisruption
```
