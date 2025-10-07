---
tags:
  - k8s/labels
---
Selectors are used to match resources labeled in a specific way, while resources can be labeled with key and value pairs. 

## How Manifest Labels Map to Services

For a service where you define the pods label in the metadata section:
```yaml
apiversion: v1
kind: Pod
metadata:
  name: pod1
  labels:
    app: app1
```

Where `app1` is the label defined for our pod that'll be mapped to the selector section in our [ClusterIP Manifest File](clusterip.md) like so:
```yaml
selector:
  app: app1
  type: front-end-app
```