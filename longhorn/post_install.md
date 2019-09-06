## Using Longhorn persistent volumes

Create a persistent volume in a file called something like `pv.yaml` with:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
  namespace: apps
  labels:
    name: mysql-data
    type: longhorn
spec:
  capacity:
    storage: 5G
  volumeMode: Filesystem
  storageClassName: longhorn
  accessModes:
    - ReadWriteOnce
  csi:
    driver: io.rancher.longhorn
    fsType: ext4
    volumeAttributes:
      numberOfReplicates: '2'
      staleReplicaTimeout: '20'
    volumeHandle: mysql-data
```

And create a claim to that volume (like an abstract request so that something can use the volume) in something like `pv-claim.yaml`:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    type: longhorn
    app: example
spec:
  storageClassName: longhorn
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```