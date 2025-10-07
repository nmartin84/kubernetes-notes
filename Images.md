---
tags:
  - k8s/images
  - k8s/containers
---
Packaged application artifacts that Pods run — most often **container images** (usually from Docker/OCI registries).

## What an Image Is

- A **container image** is a bundle of:
    - Application code + dependencies
    - Runtime environment (like Python, Node.js, Java, etc.)
    - Metadata (entrypoint, environment defaults)
- Built following the **OCI (Open Container Initiative)** format (Docker images are OCI-compliant).
- Stored in a container registry (Docker Hub, GitHub Container Registry, AWS ECR, GCP Artifact Registry, etc.).

## Example Pod with an image

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello
spec:
  containers:
    - name: web
      image: nginx:1.25
      ports:
        - containerPort: 80
```

## Image Sources

- Public registries (e.g., Docker Hub: `nginx:latest`)
- Private registries (require **imagePullSecrets** for auth):
```yaml
spec:
  imagePullSecrets:
    - name: myregistry-secret
```