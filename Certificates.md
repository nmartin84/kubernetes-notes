---
tags:
  - k8s/certs
---
Kubernetes relies heavily on **X.509 certificates** (TLS certificates) for authentication and secure communication.  

A certificate is essentially:
- A **public key** + identity (CN, O, SANs).
- Signed by a trusted **Certificate Authority (CA)**.

> [!INFO] PKI Files
> **kubeadm** automatically generates and rotates the needed PKI files under `/etc/kubernetes/pki`.

## Where Certificates are Used in Kubernetes

- **Cluster components**
    - **API Server**: Serves HTTPS, validates clients.
    - **kubelet**: Authenticates to API server.
    - **etcd**: Uses mutual TLS between peers and clients.
    - **Controller Manager / Scheduler**: Authenticate to API server.
- **Users and Service Accounts**
    - A user can authenticate with a **client certificate** signed by the cluster CA.
    - ServiceAccounts usually use tokens (JWT), but you can also use certs.
- **Add-ons / Webhooks**
    - Admission webhooks, Ingress controllers, and custom controllers often require TLS certs.

## Chain of Trust

Is a hierarchical structured certificate trust system where the certificates are issued by a higher trusted Certificate Authority (CA).
- **Public CA:** These are trusted
- **Private CA:** Not trusted but are used for internal networks

## Certificate Management

```sh
kubectl get csr
```

## Example Manifest File

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: example-csr
spec:
  signerName: kubernetes.io/kube-apiserver-client
  request: <base64-encoded-CSR>
  usages:
    - client auth

```

## Wildcard Certificates

Certificates that use wildcards to apply against multiple endpoints based on some wildcard (*.mydomain.com)