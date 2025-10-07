---
tags:
  - k8s/permissions
  - k8s/networking
---
Allows restricting network communication between pods or even possibly nodes. Policies apply to the pods and controls the inbound & outbound traffic for a [Pod](pod.md).

## Network Policy Endpoints

These are the endpoints where these rules get implemented; these utilize **Pod label matching, Pods within a namespace, or IP Addresses**.

## Manifest File

Block all network traffic
```yaml
apiVersion: networing.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy1
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: webapp1
  policyTypes:
    - Ingress
    - Egress
```

Allow inbound and outbound traffic by labels
```yaml
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
      - podSelector:
        matchLabels:
          app: webfrontend
  egress:
    - to:
      podSelector:
        matchLabels:
          app: webfrontend
```

## Configuring Network Policy Manfest for Production

```yaml
apiVersion: networing.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy1
  namespace: default
spec:
  podSelector:
    matchLabels:
      environment: production
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
      - podSelector:
        matchLabels:
          environment: sandbox
  egress:
    - to:
      - podSelector:
        matchLabels:
          environment: sandbox
```