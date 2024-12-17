
*******************************************************************************************************************************************************
#### Task 1 : Create a new ClusterRole named deployment-clusterrole, which only allows to create the following resource types:
* Deployment
* Stateful Set
* DaemonSet
Create a new ServiceAccount named cicd-token in the existing namespace app-team1.
Bind the new ClusterRole deployment-clusterrole to the new ServiceAccount cicd-token, limited to the namespace app-team1.

````
kubectl create clusterrole deployment-clusterrole --verb=create --resource=Deployments,Stateful Set,DaemonSet
kubectl create sa cicd-token -n app-team1
kubectl create clusterrolebinding deploy-b --clusterrole=deployment-clusterrole  --serviceaccount=app1-team1:cicd-token
````
**********************************************************************************************************************************************************
#### Task 2 : Set the node named ek8s-node-0 as unavailable and reschedule all the pods running on it.
```
k cordon ek8s-node-0 --ignore-daemonsets --delete-emptydir-data
k drain node ek8s-node-0
```
**********************************************************************************************************************************************************
#### Task 3 : Given an existing Kubernetes cluster running version 1.22.1, upgrade all of the Kubernetes control plane and node components on the master node only to version 1.22.2.
Be sure to drain the master node before upgrading it and uncordon it after the upgrade.

```

```
***********************************************************************************************************************************************************
#### Task 4: First, create a snapshot of the existing etcd instance running at https://127.0.0.1:2379, saving the snapshot to /var/lib/backup/etcd-snapshot.db.
example 
```
ETCDCTL_API=3 etcdctl --endpoints=https://172.30.1.2:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt  --key=/etc/kubernetes/pki/etcd/server.key  snapshot save /var/lib/backup/etcd-snapshot.db
```
************************************************************************************************************************
#### Task 5: Create a new NetworkPolicy named allow-port-from-namespace in the existing namespace fubar.
Ensure that the new NetworkPolicy allows Pods in namespace internal to connect to port 9000 of Pods in namespace fubar.
Further ensure that the new NetworkPolicy:
* does not allow access to Pods, which don't listen on port 9000
* does not allow access from Pods, which are not in namespace internal
```
kubectl label namespace internal app=my-project

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
spec:
  policyTypes:
  - Ingress
  - from:
    - namespaceSelector:
        matchLabels:
        app: my-project
    ports:
    - protocol: TCP
      port: 9000
```
**************************************************************************************************************************
#### Task 6 : Reconfigure the existing deployment front-end and add a port specification named http exposing port 80/tcp of the existing container nginx.
Create a new service named front-end-svc exposing the container port http.
Configure the new service to also expose the individual Pods via a NodePort on the nodes on which they are scheduled.

```
k get deployments
k expose pod podname --service service --port=80
```
************************************************************************************************************************
#### Task 7 : Scale the deployment presentation to 3 pods.

k scale deployment presentation --replicas=2
*************************************************************************************************************************
#### Task 8: Schedule a pod as follows:
Name: nginx-kusc00401
* Image: nginx
* Node selector: disk=ssd
```
k run nginx-kusc00401 --image=nginx --dry-run=client -o yaml
```
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-kusc00401
  name: nginx-kusc00401
spec:
  containers:
  - image: nginx
    name: nginx-kusc00401
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  nodeSelector:
    disk: ssd
status: {}
            
```
```
controlplane $ k get nodes --show-labels
NAME           STATUS   ROLES           AGE   VERSION   LABELS
controlplane   Ready    control-plane   10d   v1.31.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=controlplane,kubernetes.io/os=linux,node-role.kubernetes.io/control-plane=,node.kubernetes.io/exclude-from-external-load-balancers=
node01         Ready    <none>          10d   v1.31.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,disk=ssd,kubernetes.io/arch=amd64,kubernetes.io/hostname=node01,kubernetes.io/os=linux
controlplane $ k run nginx-kusc00401 --image=nginx --dry-run=client -o yaml > pod.yaml

controlplane $ k get pods -o wide 
NAME              READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
nginx-kusc00401   1/1     Running   0          99s   192.168.1.4   node01   <none>           <none>
controlplane $ 
```
************************************************************************************************************************
#### Task 9: 
Check to see how many nodes are ready (not including nodes tainted NoSchedule) and write the number to /opt/KUSC00402/kusc00402.txt.
```
kubectl get nodes | awk '{print $2}' | sed '1d' > /opt/KUSC00402/kusc00402.txt
```
**************************************************************************************************************************
#### Task 10: 
Task -
Schedule a Pod as follows:
✑ Name: kucc8
✑ App Containers: 2
✑ Container Name/Images:
- nginx
- consul
```
k run kucc8 --image=nginx --dry-run=client -o yaml > p.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: kucc8
  name: kucc8
spec:
  containers:
  - image: nginx
    name: nginx
  - image: consul
    name: consul
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
k create -f p.yaml
```
***********************************************************************************************************************************
#### Task 11:
Create a persistent volume with name app-data, of capacity 2Gi and access mode ReadOnlyMany. The type of volume is hostPath and its location is /srv/app-data.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-data
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadOnlyMany
  volumeMode: Block
  hostPath:
   path: /srv/app-data

k create -f pv.yaml 
persistentvolume/app-data created
controlplane $ vi pv.yaml
controlplane $ k get pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
app-data   2Gi        ROX            Retain           Available                          <unset>                          27s

```
**************************************************************************************************************************************
#### Task 12:
Monitor the logs of pod foo and:
✑ Extract log lines corresponding to error file-not-found
✑ Write them to /opt/KUTR00101/foo
```
k logs -f foo | grep -i file-ot-found > /opt/KUTR00101/foo
```
***************************************************************************************************************************************
#### Task 13:
An existing Pod needs to be integrated into the Kubernetes built-in logging architecture (e.g. kubectl logs). Adding a streaming sidecar container is a good and common way to accomplish this requirement.
Add a sidecar container named sidecar, using the busybox image, to the existing Pod big-corp-app. The new sidecar container has to run the following command

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: big-corp-app
  name: big-corp-app
spec:
  containers:
  - image: busybox
    name: big-corp-app
    command: ["/bin/sh", "-c"]
    args: [ while true; do echo "sleep"; done  > /var/log/big-corp-app.log ]
    volumeMounts:
    - mountPath: /var/log/
      name: cache-volume
  - image: busybox
    name: sidecar
    command: ["/bin/sh", "-c"]
    args: [ tail -n+1 -f /var/log/big-corp-app.log ]
    volumeMounts:
    - mountPath: /var/log/
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir:
      sizeLimit: 500Mi
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
**********************************************************************************************************************************
#### Task 14
Task -
From the pod label name=overloaded-cpu, find pods running high CPU workloads and write the name of the pod consuming most CPU to the file /opt/
KUTR00401/KUTR00401.txt (which already exists).

k top pods 









***************************************
#### Task 15 :A Kubernetes worker node, named wk8s-node-0 is in state NotReady.
Investigate why this is the case, and perform any appropriate steps to bring the node to a Ready state, ensuring that any changes are made permanent.
```
systemctl enable --now kubelet
systemctl start kubelet.service
systemctl enable kubelet.service
```
***************************************
#### Task 16 : Create a new PersistentVolumeClaim:
✑ Name: pv-volume
✑ Class: csi-hostpath-sc
✑ Capacity: 10Mi
Create a new Pod which mounts the PersistentVolumeClaim as a volume:
✑ Name: web-server
✑ Image: nginx
✑ Mount path: /usr/share/nginx/html
Configure the new Pod to have ReadWriteOnce access on the volume.
Finally, using kubectl edit or kubectl patch expand the PersistentVolumeClaim to a capacity of 70Mi and record that change.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume
  labels:
    type: local
spec:
  storageClassName: csi-hostpath-sc
  capacity:
    storage: 10Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/usr/share/nginx/html"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-claim
spec:
  storageClassName: csi-hostpath-sc
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi
---
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  volumes:
    - name: pv-claim
      persistentVolumeClaim:
        claimName: pv-claim
  containers:
    - name: web-server
      image: nginx
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: pv-claim
```

