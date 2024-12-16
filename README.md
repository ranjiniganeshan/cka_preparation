# cka_preparation

## Day 1
### Kubernetes Architecture

![image](https://github.com/user-attachments/assets/335d6cab-9977-4083-b65c-6f97806b5561)
* Control plane
   * Kubeapiserver : Responsible for sending api calls to kubelet for managing the orchestration
   * ETCD - key value pairs for storing the cluster information
   * kube controller manager - Responsible for managing the nodes pods (node controller)
   * kubec scheduler - based on the pod request schedules the pod to worker nodes
* worker nodes
   * kubelet - communicates with kubeapi to recieve the api calls and monitors the status of the pod running on the worker node
   * kube-proxy - communication between the pods running on the worker nodes.

* runtime can be docker or container runtime or rkt

### Docker deprecated from version 1.24
* containerd is used. cli commands for container operation refer https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md

### Create a new pod with the name redis and with the image redis123
```
kubectl run redis --image=redis123 --dry-run=client -o yaml
kubectl create -f pod.yaml
```

```
https://kubernetes.io/docs/reference/kubectl/conventions/

Create an NGINX Pod

kubectl run nginx --image=nginx
Generate POD Manifest YAML file (-o yaml). Don’t create it(–dry-run)

kubectl run nginx --image=nginx --dry-run=client -o yaml
Create a deployment

kubectl create deployment --image=nginx nginx
Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run)

kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run) and save it to a file.

kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
Make necessary changes to the file (for example, adding more replicas) and then create the deployment.

kubectl create -f nginx-deployment.yaml
OR

In k8s version 1.19+, we can specify the –replicas option to create a deployment with 4 replicas.


kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > 

```
Imperative 

<img width="706" alt="Screenshot 2024-12-01 at 5 45 39 PM" src="https://github.com/user-attachments/assets/3f06cd95-039b-4460-a2fc-84d319390fc9">


** Take a backup of the etcd cluster and save it to /opt/etcd-backup.db
```
k describe pod etcd-controlplane -n kube-system
Name:                 etcd-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 controlplane/192.14.88.8
Start Time:           Mon, 16 Dec 2024 14:43:56 +0000
Labels:               component=etcd
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.14.88.8:2379
                      kubernetes.io/config.hash: 39c4efaab7c3beb6194767c9e5c82d66
                      kubernetes.io/config.mirror: 39c4efaab7c3beb6194767c9e5c82d66
                      kubernetes.io/config.seen: 2024-12-16T14:43:55.327868488Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.14.88.8
IPs:
  IP:           192.14.88.8
Controlled By:  Node/controlplane
Containers:
  etcd:
    Container ID:  containerd://f6003ae318fbb1aa86b37691b56d8123e2f5774a7500091002797624a090f2b8
    Image:         registry.k8s.io/etcd:3.5.15-0
    Image ID:      registry.k8s.io/etcd@sha256:a6dc63e6e8cfa0307d7851762fa6b629afb18f28d8aa3fab5a6e91b4af60026a
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --advertise-client-urls=https://192.14.88.8:2379
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --experimental-initial-corrupt-check=true
      --experimental-watch-progress-notify-interval=5s
      --initial-advertise-peer-urls=https://192.14.88.8:2380
      --initial-cluster=controlplane=https://192.14.88.8:2380
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://192.14.88.8:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://192.14.88.8:2380
      --name=controlplane
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --snapshot-count=10000
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    State:          Running
      Started:      Mon, 16 Dec 2024 14:43:46 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
      memory:     100Mi
    Liveness:     http-get http://127.0.0.1:2381/livez delay=10s timeout=15s period=10s #success=1 #failure=8
    Readiness:    http-get http://127.0.0.1:2381/readyz delay=0s timeout=15s period=1s #success=1 #failure=3
    Startup:      http-get http://127.0.0.1:2381/readyz delay=10s timeout=15s period=10s #success=1 #failure=24
    Environment:  <none>
    Mounts:
      /etc/kubernetes/pki/etcd from etcd-certs (rw)
      /var/lib/etcd from etcd-data (rw)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  etcd-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/pki/etcd
    HostPathType:  DirectoryOrCreate
  etcd-data:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/etcd
    HostPathType:  DirectoryOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute op=Exists
Events:
  Type    Reason   Age   From     Message
  ----    ------   ----  ----     -------
  Normal  Pulled   10m   kubelet  Container image "registry.k8s.io/etcd:3.5.15-0" already present on machine
  Normal  Created  10m   kubelet  Created container etcd
  Normal  Started  10m   kubelet  Started container etcd

ETCDCTL_API=3 etcdctl --endpoints="https://192.14.88.8:2379"  --cert
="/etc/kubernetes/pki/etcd/server.crt" --key="/etc/kubernetes/pki/etcd/server.key" --
cacert="/etc/kubernetes/pki/etcd/ca.crt" snapshot save /opt/etcd-backup.db
Snapshot saved at /opt/etcd-backup.db

```
** Create a Pod called redis-storage with image: redis:alpine with a Volume of type emptyDir that lasts for the life of the Pod.
Specs on the below.
Pod named 'redis-storage' created
Pod 'redis-storage' uses Volume type of emptyDir
Pod 'redis-storage' uses volumeMount with mountPath = /data/redis
```
k run redis-storage --image=redis:alpine --dry-run=client -o yaml >redis.yaml

controlplane ~ ➜  vi redis.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis-storage
  name: redis-storage
spec:
  containers:
  - image: redis:alpine
    name: redis-storage
    resources: {}
    volumeMounts:
     - mountPath: /data/redis
       name: redis-storage
  volumes:
  - name: redis-storage
    emptyDir:
      sizeLimit: 500Mi
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

controlplane ~ ➜  k apply -f redis.yaml 
pod/redis-storage created

controlplane ~ ➜  k get pod redis-storage
NAME            READY   STATUS    RESTARTS   AGE
redis-storage   1/1     Running   0          18s

```

