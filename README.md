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
![Uploading Screenshot 2024-12-01 at 5.45.39 PM.png…]()

kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > 

```

