---
tags:
  - k8s/permissions
  - k8s/access
---
A **ServiceAccount** in Kubernetes is an **identity for processes that run inside Pods**.

Whereas a _user account_ represents a human (like an admin or developer), a **service account** is meant for automation: workloads, controllers, or apps inside the cluster.

## What a ServiceAccount is

- It’s a Kubernetes object (`kind: ServiceAccount`) living in a namespace.
- Each Pod runs as _some_ service account (defaults to `default` in its namespace).
- The service account’s identity can be used with **RBAC** to grant only the permissions that Pod needs.

## What it provides to Pods

1. **Credentials for the API server**
    - Historically: an auto-mounted token (`/var/run/secrets/kubernetes.io/serviceaccount/token`).
    - Modern clusters: short-lived **JWT projected tokens** (`BoundServiceAccountTokenVolume`).
2. **Associated RBAC permissions**
    - Bound to `Role`/`ClusterRole` via `RoleBinding`/`ClusterRoleBinding`.
3. **Namespace scoping**
    - A ServiceAccount belongs to a single namespace.

## Manifest File

```yaml
# 1. Define a ServiceAccount
apiVersion: v1
kind: ServiceAccount
metadata:
  name: myapp-sa
  namespace: dev

---
# 2. Bind RBAC permissions (read Pods)
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
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: ServiceAccount
  name: myapp-sa
  namespace: dev
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io

---
# 3. Use the ServiceAccount in a Pod
apiVersion: v1
kind: Pod
metadata:
  name: app-using-sa
  namespace: dev
spec:
  serviceAccountName: myapp-sa
  containers:
  - name: myapp
    image: curlimages/curl
    command: ["sleep", "3600"]
```

## Manage Service Accounts

```sh
# Get Service Accounts
kubectl get serviceaccount

# Create Service Accounts
TOKEN=$(kubectl create token svcacct1)
kubectl config set-credentials svcacct1 --token=$TOKEN
kubectl config set-context new-context1 --cluster=default --user=svcacct1
kubectl use-context new-context1
```