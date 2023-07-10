# Minikube  

This is my quick notes on [Minikube](https://minikube.sigs.k8s.io/docs/), a Kubernetes platform built for testing / learning Kubernetes. I dont think its meant for production, but for learning Kubernetes? This is fine.  

!> Minikube cannot run by itself - you will also need to install [kubectl](operating_systems/kubernetes/kubectl?id=install) and something that can deploy containers (the most popular one, and the one I use, is [Docker](operating_systems/docker/docker_basics?id=docker-engine-installation-ubuntu)).  

# Install  

## Minikube Install  

> The Minikube install instructions are [here](https://minikube.sigs.k8s.io/docs/start/). 

For Linux, this is:  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Get the install file: 
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```  

3\. Install Minikube:  
```
install minikube-linux-amd64 /usr/local/bin/minikube
```  

> This is the minimum install - you can go further and [start the cluster](operating_systems/kubernetes/minikube?id=starting-a-cluster); if you want to go even further and test to see if its working, you can [follow these instructions](https://minikube.sigs.k8s.io/docs/start/) (start on #4 if you have already started the Minikube cluster). 

# Other Installs  

You will also need to install [Docker](operating_systems/docker/docker_basics?id=docker-engine-installation-ubuntu) and [kubectl](operating_systems/kubernetes/kubectl?id=install).  

# Starting a Cluster  

To start a [Kubernetes cluster](operating_systems/kubernetes/kubernetes_basics?id=cluster) via Minikube, run:  
```
minikube start 
```  
* This should not be run as root, but by an account that has root access.  
   * That said, I was able to run this on an account that does not have root access with no warnings or errors, but I am not fully sure what consequences this will hold.  
* If you _must_ run as root, you can run the above with `--force`.  

# Kubernetes Pods in Minikube  

A typical listing of pods required to support a Kubernetes instance via Minikube are:  
```
NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
kube-system   coredns-787d4945fb-6pmmq           1/1     Running   0             81m
kube-system   etcd-minikube                      1/1     Running   0             81m
kube-system   kube-apiserver-minikube            1/1     Running   0             81m
kube-system   kube-controller-manager-minikube   1/1     Running   0             81m
kube-system   kube-proxy-qj8hf                   1/1     Running   0             81m
kube-system   kube-scheduler-minikube            1/1     Running   0             81m
kube-system   storage-provisioner                1/1     Running   1 (81m ago)   81m
```  
* This can be shown by the [kubectl get pods -A](operating_systems/kubernetes/kubectl?id=get-pods) command.  
