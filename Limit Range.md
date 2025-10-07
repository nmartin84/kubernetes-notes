---
tags:
  - k8s/quotas
---
A `LimitRange` in Kubernetes is a **policy applied at the namespace level** that sets **default, minimum, and maximum resource usage for Pods and containers**.
## Managing Limit Range Objects

You can get this object detail by running `kubectl get limitrange` to get a list of all your limit ranges you have defined.

```sh
kubectl get limitrange
```

## Manifest File

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-cpu-limits
  namespace: dev-team
spec:
  limits:
    - type: Container
      default:
        cpu: "500m"
        memory: "512Mi"
      defaultRequest:
        cpu: "250m"
        memory: "256Mi"
      min:
        cpu: "100m"
        memory: "128Mi"
      max:
        cpu: "1"
        memory: "1Gi"
```
- **default**: If a container doesn’t set a limit → it gets **0.5 CPU, 512Mi memory**.
- **defaultRequest**: If a container does not set a request → it gets **0.25 CPU, 256Mi memory**.
- **min**: A container must ask for **at least 0.1 CPU and 128Mi memory**.
- **max**: A container can’t ask for more than **1 CPU and 1Gi memory**.

## Types of LimitRange

- `Container` → applies to individual containers in a Pod.
- `Pod` → applies to the whole Pod (sum of containers).
- `PersistentVolumeClaim` → controls min/max/default PVC storage sizes.

## Why It’s Useful

- Prevents “noisy neighbor” problems (one Pod taking all resources).
- Ensures workloads request realistic resources (improves scheduler efficiency).
- Provides sensible defaults, so developers don’t need to set everything manually.