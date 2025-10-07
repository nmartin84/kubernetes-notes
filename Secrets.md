---
tags:
  - k8s/secrets
---
Allows the store and passing of secrets to pods in their deployment manifest file. 

## Create Secret From File

```sh
kubectl create secret generic db-creds --from-file=./username.txt --from-file=./password.txt
```

## Manifest File

When defining [Pod](pod.md) you can utilize this method to map secrets to the pod:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-from-manifest
type: Opaque
data:
  username: YWRt=4f
  password: MWYzfkljRTOIF2R2M
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx-db-secret
spec:
  containers:
    - name: nginx-secret
      image: nginx
      env:
        - name: USERNAME
          valueFrom:
            secretKeyRef:
              name: secret-from-manifestfile
              key: username
        - name: PASSWORD
          valueFrom:
            secretKeyRef:
              name: secret-from-manifestfile
              key: password
```