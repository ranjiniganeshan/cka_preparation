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



