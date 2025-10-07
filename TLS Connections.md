---
tags:
  - k8s/tls
  - k8s/secrets
---
Various versions of TLS exist and must be considered when leveraging TLS technology, as some older versions are now vulnerable to security flaws.

## TLS Communication Flow

1. Client contacts server on  TCP port 443 and presents its supported ciphers.
2. Client and server agree on cipher suite.
3. Server sends client its PKI certificate (with public key)
4. Client confirms certificate validility and generates unique session key, encrypts with server public key, and sends to the server.
5. Server decrypts unique session key with its private key