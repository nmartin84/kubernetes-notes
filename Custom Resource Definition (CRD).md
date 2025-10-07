---
tags:
  - k8s/api
  - k8s/extensibility
  - k8s/operators
---
A CustomResourceDefinition (CRD) lets you add new resource types to the Kubernetes API server so you can store and manage custom objects just like built-in resources.

## Building Blocks

- Define `apiVersion`, `kind`, and schema (OpenAPI v3) for validation.
- Set `scope` (`Namespaced` or `Cluster`) and naming (`singular`, `plural`, `kind`, `shortNames`).
- Versioning supports `served` and `storage` flags plus conversion webhooks for upgrades.
- Custom controllers/operators watch these resources and implement domain logic.

> [!NOTE] RBAC & Backups
> Grant API access to the new `apiGroup` and ensure cluster backups capture CRDs plus their instances.

## Example CRD

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: databases.infrastructure.example.com
spec:
  group: infrastructure.example.com
  names:
    kind: Database
    plural: databases
    singular: database
    shortNames:
    - db
  scope: Namespaced
  versions:
  - name: v1alpha1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            required:
            - engine
            - size
            properties:
              engine:
                type: string
              size:
                type: string
          status:
            type: object
            properties:
              phase:
                type: string
```

## Using the New Resource

```yaml
apiVersion: infrastructure.example.com/v1alpha1
kind: Database
metadata:
  name: orders-db
spec:
  engine: postgres
  size: small
```

## Operational Snippets

```sh
# Apply the CRD then create instance objects
kubectl apply -f database-crd.yaml
kubectl apply -f orders-db.yaml

# Inspect the new resource
kubectl get databases.infrastructure.example.com

# Describe schema and accepted versions
kubectl explain database.spec
```
