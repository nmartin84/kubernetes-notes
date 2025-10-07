---
tags:
  - k8s/nodes
---
In Kubernetes, `hostPID` is a security context setting within a Pod's specification that determines whether the Pod's containers will share the host's Process ID (PID) namespace.

## Functionality

- When `hostPID: true` is set in a Pod's `spec`, the containers within that Pod will see and interact with the processes running directly on the host node, as if they were running natively on the host. This means:
	- Running `ps` from inside such a container will display all processes on the host, not just those within the container's own PID namespace.
	- Containers can potentially send signals to or even kill processes on the host.
- The default value for `hostPID` is `false`, meaning containers operate within their own isolated PID namespace and cannot see or interact with host processes directly.

## Use Cases

- While `hostPID` poses security risks, it is sometimes necessary for specific system-level applications or monitoring tools that require visibility into or interaction with host processes. Examples include:
	- Node-level monitoring agents that need to collect performance data from the host.
	- Security agents that need to inspect host processes for malicious activity.