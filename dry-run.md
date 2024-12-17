
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
