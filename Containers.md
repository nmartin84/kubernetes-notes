---
tags:
  - k8s/containers
  - k8s/pods
---
These are lightweight, executable packages of software that include everything needed to run an application, such as code, runtime, system tools, libraries, and settings. They virtualize the operating system, ensuring applications run consistently across different environments.

- **Isolation** Containers run isolated from each other and the host system, sharing the host OS kernel, but otherwise operating in their own isolated environment.  
- **Portability**: Since containers carry all their dependencies, they can run consistently across any computing environment, from a personal laptop to a public cloud or a hybrid setup.
- **Resource Efficiency** Containers require less overhead than traditional virtual machines because they do not include operating system images. This makes them more efficient in terms of system resource usage.

## Manifest File

There is no resource object specifically for a Container. Instead you have to define a manifest file for a Deployment, Pod, or some other resource that will leverage a container.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
  labels:
    app: demo
spec:
  containers:
    - name: my-container
      image: nginx:1.27   # container image
      ports:
        - containerPort: 80
      resources:          # optional resource requests/limits
        requests:
          cpu: "100m"
          memory: "128Mi"
        limits:
          cpu: "200m"
          memory: "256Mi"
      env:                # optional environment variables
        - name: APP_ENV
          value: "production"
      volumeMounts:       # optional volume mount
        - name: app-data
          mountPath: /usr/share/nginx/html
  volumes:                # define volumes at Pod level
    - name: app-data
      emptyDir: {}        # simple ephemeral volume
```

## Multi-Container Manifest File

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  volumes:
    - name: log-files
      emptyDir: {}
  containers:
  - name: web-server
    image: nginx
    volumeMounts:
      - name: log-files
        mountPath: /var/log/nginx
  - name: web-logger
    image: busybox:1.30.1
    command: ['sh', '-c', 'while true; do cat /var/log/nginx/access.log; sleep 30; done']
    volumeMounts:
      - name: log-files
        mountPath: /var/log/nginx
```