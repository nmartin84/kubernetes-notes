Represents the smallest and most fundamental deployable unit of computing that can be created and managed. It is an abstraction that encapsulates one or more containers, along with shared resources and a specification for how to run those containers

- Encapsulation of Containers
- Run a single main container (except in advanced scenarios)
- Pods are ephemeral by nature
- Shared Resources
- Unique IP Addresses (ephemeral by nature)
- Managed by higher constructs like Deployments and ReplicaSets


> [!NOTE] Management
> Managed as a unit, Pods are usually managed by higher-level Kubernetes constructs like Deployments or ReplicaSets.

## Usage

Pods encapsulate an application’s container (or containers), storage resources, a unique network IP, and options that govern how the container(s) should run. They represent a “logical host” for one or more containers that share the same context.

## Imperative Deploy Pod

```sh
kubectl run --image=nginx:latest nginx-pod1
```

## Command Argument

Command argument allows you to launch the pod and execute a command. This can override the command issued by your dockerfile.

```yaml
command: ['sh', '-c', 'while true; do date; sleep 3; done'
```



## imagePullPolicy

You have the option to specify the image pull policy, which will tell it to either always pull the [Images](images.md) (ensures it has the latest updates) or just pull if it does not exist. 

## Default Restart Policy

Restart Policy by default is set to **Always**

## File Copy with Pods

```sh
kubectl cp /tmp/foo <some-pod>:/tmp/bar -c <specific-container>
kubectl cp hello-world.txt busypod-pod:/ busybox-container
```
- see [kubectl cp | Kubernetes](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_cp/)

## Print Env Variables

```sh
k exec podname -- printenv
```



