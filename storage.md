## Storage
1. Configure a volume to store these logs at /var/log/webapp on the host.
Use the spec provided below.
Name: webapp
Image Name: kodekloud/event-simulator
Volume HostPath: /var/log/webapp
Volume Mount: /log
##### reference https://kubernetes.io/docs/concepts/storage/volumes/#hostpath
```
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: webapp
    image: kodekloud/event-simulator
    volumeMounts:
    - mountPath: /log
      name: webapp
  volumes:
  - name: webapp
    hostPath:
      path: /var/log/webapp
      type: Directory # this field is optional
```
```
k replace --force -f webapp.yaml 
```
2. Create a Persistent Volume with the given specification.
Volume Name: pv-log
Storage: 100Mi
Access Modes: ReadWriteMany
Host Path: /pv/log
Reclaim Policy: Retain

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  persistentVolumeReclaimPolicy: Retain
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 100Mi
  hostPath:
    path: /pv/log
```
```
k create -f pv.yaml
```
3. Let us claim some of that storage for our application. Create a Persistent Volume Claim with the given specification.

Persistent Volume Claim: claim-log-1
Storage Request: 50Mi
Access Modes: ReadWriteOnce
                                               
###### reference https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
```
```
k create -f pvc.yaml
```
