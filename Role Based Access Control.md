---
tags:
  - k8s/access
  - k8s/security
feature: Pasted image 20251006093535.png
---
**Role-Based Access Control (RBAC)** in Kubernetes is the system that controls **who can do what** in the cluster.

It answers three key questions:

- **Who** (user, group, or service account)
- **Can do what** (verbs like get, list, create, delete)
- **On which resources** (Pods, Deployments, ConfigMaps, etc.)
- **In which scope** (namespace or entire cluster)

## Key RBAC Objects

1. **Role**
    - Defines a set of permissions (rules) **within a namespace**.
    - Example: allow listing Pods in `dev` namespace.
2. **ClusterRole**
    - Like Role, but cluster-wide.
    - Can apply across all namespaces or to non-namespaced resources (like Nodes).
3. **RoleBinding**
    - Binds a Role to a user, group, or service account **in a specific namespace**.
4. **ClusterRoleBinding**
    - Binds a ClusterRole to a subject across the entire cluster.

## Manifest File

```yaml
# Role: allow listing Pods in dev namespace
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
---
# RoleBinding: grant the Role to a user
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
  - kind: User
    name: alice
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```
- Role `pod-reader` allows read access to Pods.
- RoleBinding gives that Role to **user `alice`** in the `dev` namespace.

## Mappings

![[Pasted image 20251006093535.png]]

## Common Verbs

- `get`, `list`, `watch` → read operations
- `create`, `update`, `patch`, `delete` → write operations

## Managing Roles

```sh
# Roles
kubectl get role -n namespace
kubectl describe role pod-manager -n namespace | more

# ClusterRoles
kubectl get clusterrole
kubectl describe clusterrole cr1 | more
```
