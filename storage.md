## Storage
1. Configure a volume to store these logs at /var/log/webapp on the host.
Use the spec provided below.
Name: webapp
Image Name: kodekloud/event-simulator
Volume HostPath: /var/log/webapp
Volume Mount: /log
## reference https://kubernetes.io/docs/concepts/storage/volumes/#hostpath
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
                                               
