---
tags:
  - k8s/storage
  - k8s/permissions
---
Determined the level of permissions permitted for the storage on a given container. 

**Node Level Changes**
- **RWO**: Only a single node in a cluster can access this storage path with read/write permissions.
- **ROX**: Allows many connections with read only access to the storage path.
- **RWX**: Multiple connections with read and write permissions (comes with risks since multiple containers are accessing and writing to the storage).

**Pod level changes**
- **RWOP**: Allows a single pod access for read and write permissions.

