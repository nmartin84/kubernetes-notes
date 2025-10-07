---
tags:
  - k8s/certs
---
**PKI (Public Key Infrastructure)** is the system of using public/private key pairs and digital certificates to establish trust and secure communication.

**In Kubernetes, PKI is used to:**
- Prove identity of cluster components (API server, kubelet, etcd, controllers).
- Encrypt traffic over TLS (HTTPS, gRPC).
- Sign and validate requests (e.g., clients to API server).

## PKI Lifecycle

1. Certificate Signing Request
2. Issuance
3. Use
4. Revocation
5. Renewal/Expiration