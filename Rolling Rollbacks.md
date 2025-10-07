---
tags:
  - k8s/updates
---
A **rollback** is reverting a Deployment to a previous revision if something goes wrong (e.g., new version crashes).

- Kubernetes keeps a history of Deployment revisions.
- If a new rollout fails, you can rollback automatically or manually.

## Manual rollback

```sh
# Rollback to the previous revision
kubectl rollout undo deployment web

# Rollback to a specific revision
kubectl rollout undo deployment web --to-revision=2
```

## Monitoring rollout progress

```sh
kubectl rollout status deployment web
```