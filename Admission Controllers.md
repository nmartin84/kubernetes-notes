---
tags:
  - k8s/api
  - k8s/security
  - k8s/extensibility
---
Admission controllers enforce custom logic on API requests after authentication/authorization but before objects persist. They can mutate requests, validate policy, or deny operations outright.

## Types of Admission Control

- Built-in controllers compiled into the API server (e.g., `NamespaceLifecycle`, `PodSecurity`, `LimitRanger`).
- Dynamic webhooks: `MutatingAdmissionWebhook` (runs first) and `ValidatingAdmissionWebhook` (runs second) call external HTTPS services.
- Policy-based: `ValidatingAdmissionPolicy` lets clusters declare CEL expressions for validation without webhooks.

> [!SECURITY] Harden Webhooks
> Serve on TLS, restrict network access, and set `failurePolicy: Fail` for critical policies so requests stop if the webhook is unreachable.

## Request Flow

1. User submits request (`kubectl apply`, controller reconciliation, etc.).
2. Authentication & authorization succeed.
3. Mutating admission controllers sequentially adjust the object.
4. Validating admission controllers ensure the final object meets policy.
5. Object persists to etcd if all controllers approve.

## Webhook Example

```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
  name: disallow-default-namespace
webhooks:
- name: gatekeeper.default-ns.example.com
  clientConfig:
    service:
      namespace: policy
      name: policy-webhook
      path: /validate
    caBundle: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0t
  rules:
  - apiGroups: ["apps"]
    apiVersions: ["v1"]
    operations: ["CREATE", "UPDATE"]
    resources: ["deployments"]
  failurePolicy: Fail
  sideEffects: None
  admissionReviewVersions: ["v1"]
```

## Operational Snippets

```sh
# List enabled admission plugins on a control plane node
kube-apiserver -h | Select-String EnableAdmissionPlugins

# Check webhook status
kubectl get validatingwebhookconfigurations
kubectl describe validatingwebhookconfiguration disallow-default-namespace
```
