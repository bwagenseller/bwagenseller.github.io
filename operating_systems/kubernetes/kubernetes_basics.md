<!-- Is suitable for the public without this comment 

-->

# Kubernetes Basics 

## What is Kubernetes?  

[Kubernetes](https://kubernetes.io/) (also known as <font color="green">K8</font>)is an approach to deploying robust, fault tolerant, horizontally scalable applications (while also hosting all associated applications, services, and microservices as well). <font color="green">Kubernetes</font> can run on as little as one [node](operating_systems/kubernetes/kubernetes_basics?id=node) or it can run simultaneously on _thousands_ of nodes.  

<font color="green">K8s</font> can seem somewhat abstract - there is no direct '<font color="green">Kubernetes</font>' package you can install and then run <font color="green">Kubernetes</font>; instead, you install something that installs its own 'version' of <font color="green">Kubernetes</font> - for example:  
* [Minikube](operating_systems/kubernetes/minikube), which runs locally on your laptop; you can use to test out <font color="green">K8s</font> (this is the one I use primarily)  
* [Kind](https://kind.sigs.k8s.io/), which runs locally on your laptop; you can use to test out <font color="green">K8s</font>  
* [Amazon Elastic Kubernetes Service](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/amazon-elastic-kubernetes-service.html) (aka AWS EKS)  
* [Linode](https://www.linode.com/)  

'<font color="green">Kubernetes</font>' is usually not installed on your laptop (unless you are using Minikube to learn <font color="green">Kubernetes</font>).  

It should be noted that <font color="green">Kubernetes</font> is just a shell that coordinates other things to run - it doesnt _actually_ run any of your desired applications directly. Therefore, we need something to do this, and [Docker](operating_systems/docker/) is almost the universal choice for this - so Docker _must_ be installed on all [nodes](operating_systems/kubernetes/kubernetes_basics?id=node) as well (although its not required for your laptop unless you are running something like Minikube locally).  

You will also need to install [Helm](operating_systems/kubernetes/helm), which describes / lays out the instructions for <font color="green">Kubernetes</font> to execute.  

Finally, you will also need a way to interact with <font color="green">Kubernetes</font> - remember, <font color="green">Kubernetes</font> is not something you typically install locally, so you will need a way to interact with it from your local laptop. While there are other interfaces out there, [kubectl](operating_systems/kubernetes/kubectl) is the most popular - so this _must_ be installed on your laptop.  

So in order to fully utilize <font color="green">Kubernetes</font>, you will need to have an understanding of  
* Kubernetes itself.  
   * As well as an entity that actually deploys <font color="green">Kubernetes</font>, such as <font color="purple">Minikube</font>.  
* [Helm](operating_systems/kubernetes/helm)  
   * To use Helm you will need to know [YAML](learn_to_code/yaml) and at least be able to understand [Go](https://go.dev/) templates.  
* [Docker](operating_systems/docker/)  
* [kubectl](operating_systems/kubernetes/kubectl)  



# Before We Begin

## Useful Links  

* [My Docker Overview](/operating_systems/docker/)  

## Why I Do This

I find that I have to write things down, in a format I understand, in order for me to fully understand a topic; therefore I take notes on subjects I try to learn. These notes are an artifact of that line of thinking.

## My Note Sources

My main note sources are: 
* Various code examples I have seen on the web.  
* [stackoverflow](https://stackoverflow.com/)  
* [Learn Kubernetes in a Month of Lunches](https://www.manning.com/books/learn-kubernetes-in-a-month-of-lunches)  
   * [KIAMOL Exercises](https://github.com/sixeyed/kiamol)  
* [An Introduction To Kubernetes - For Beginners](https://www.youtube.com/watch?v=PlWPH_VUlNE)  
* [Kubernetes Course - Full Beginners Tutorial (Containerize Your Apps!)](https://www.youtube.com/watch?v=d6WC5n9G_sM)  
* [Beginners guide to Kubernetes](https://www.linode.com/docs/guides/beginners-guide-to-kubernetes/) on Linode  


## Docker is Critical  

You will have to understand Docker (or an equivalent) to use Kubernetes. See [my Docker overview](/operating_systems/docker/).  

## Kubectl is Critical  

You will have to understand Kubectl (or an equivalent tool) to use Kubernetes. See [my Kubectl overview](/operating_systems/kubernetes/kubectl).  

# Terminology  

## Cluster  

As defined on [kubernetes.io](https://kubernetes.io/docs/concepts/overview/components/), a <font color="green">Cluster</font> consists of a set of worker machines, called nodes, that run containerized applications. There is always at least one worker node.  

A <font color="green">Cluster</font> will direct a group of nodes, so long as all of those nodes have the same (or practically the same) goal.  

## Namespace  

According to [kubernetes.io](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/), a <font color="green">Namespace</font> provides a mechanism for isolating groups of resources within a single cluster. Its mostly used when resources in Kubernetes are spread across many users in multiple teams / projects. It is stated that different <font color="green">Namespaces</font> are typically only necessary if there are more than a few dozen users.  

> The default <font color="green">Namespace</font> is `default`, but kubernetes.io suggests _not_ using `default` in production.  

## Node  

A <font color="green">Node</font> is a 'worker machine' - this is (usually) a server, but it could also be a laptop, a desktop, etc. It doesnt even have to be a 'bare metal' machine - it could also be a virtual machine. a <font color="green">Node</font> can run multiple pods.  A <font color="green">Node</font> is responsible for providing compute resources (memory, CPU cycles, etc) to pods.  

### Master Node  

A <font color="green">Master Node</font> is a type of [node](operating_systems/kubernetes/kubernetes_basics?id=node) that is meant to be a server (bare metal or virtual) that controls the [slave nodes](operating_systems/kubernetes/kubernetes_basics?id=slave-node). 

A <font color="green">Master Node</font>:  
* Is meant to act as an administrative node / as a 'point of contact' for DevOps engineers to interact with the entire [cluster](operating_systems/kubernetes/kubernetes_basics?id=cluster).  
   * For example, all requests for pods will be sent through the <font color="green">Master Node</font>, and from there, a [slave node](operating_systems/kubernetes/kubernetes_basics?id=slave-node) will actually run the [pod](operating_systems/kubernetes/kubernetes_basics?id=pod).  
* Does not _usually_ host [pods](operating_systems/kubernetes/kubernetes_basics?id=pod) itself.  
* Manages the [slave nodes](operating_systems/kubernetes/kubernetes_basics?id=slave-node).  
* Handles authentication.  
* There are typically multiple <font color="green">Master Node</font> in a [cluster](operating_systems/kubernetes/kubernetes_basics?id=cluster).  
   * There are usually less <font color="green">Master Nodes</font> than slave nodes.  
* The <font color="green">Master Nodes</font> require less resources (CPU, memory, etc) than the slave nodes.  

### Slave Node  

A <font color="green">Slave Node</font> is a node that runs [pods](operating_systems/kubernetes/kubernetes_basics?id=pod) as a worker node; it is not a node that an administrator would directly interact with, typically.  

## Pod  

A <font color="green">Pod</font> is the smallest deployable unit in Kubernetes and is similar to a [Docker Container](operating_systems/docker/docker_basics?id=container); It could be a Docker container, but it does not _have to be a Docker container. 

Its not uncommon to have multiple pods with the _exact_ same configuration, performing the same tasks. This is done so the application the pods are supporting is horizontally scalable.  

## Container  

A <font color="green">Container</font> is an executable piece of software that can run at least a portion of an application. <font color="green">Containers</font> contain everything they need to run the application - configuration files, libraries, etc etc. A popular <font color="green">Container</font> used in Kubernetes is the [Docker container](operating_systems/docker/docker_basics?id=container).  

## Control Loop  

A <font color="green">Control Loop</font> is a system that measures and adjusts a variable that controls an individual process. In terms of Kubernetes, the <font color="green">Control Loop</font> manages the state of the [cluster](operating_systems/kubernetes/kubernetes_basics?id=cluster).  For example, there is a control loop that allows an administrator the ability to update application versions of apps running in the pod on the fly.  

## Controller  

A <font color="green">Controller</font> is a [control loop](operating_systems/kubernetes/kubernetes_basics?id=control-loop) that watches for changes coming from the [Kubernetes API](operating_systems/kubernetes/kubernetes_basics?id=kube-apiserver) (which originate from an adminstrator); the <font color="green">Controller</font> will then alter the desired state with respect to the targeted [pod(s)](operating_systems/kubernetes/kubernetes_basics?id=pod).  

## Service  

> It should be noted that the term 'service' can be pretty generic; a Kubernetes <font color="green">Service</font> is slightly more defined, as it typically means its a load balancer (at least in some form) and it also means it usually has a static IP.  

A <font color="green">Service</font> is a Kubernetes entity that provides specific services for pods. For example, a <font color="green">Service</font> can act like a load balancer, spreading traffic between pods that have the same function; all incoming traffic goes through the service. A <font color="green">Service</font> can be internal or external; an external <font color="green">Service</font> can be accessed from outside of the cluster (for example, a service that exposes an external websocket), while an internal <font color="green">Service</font> can be accessed only from inside of the cluster.  

Due to all of the above, a <font color="green">Service</font> usually has a static IP address.  

Examples of Kubernetes services:  
* [Kubeproxy](operating_systems/kubernetes/kubernetes_basics?id=kubeproxy)  

## ReplicaSet  

A <font color="green">ReplicaSet</font> maintains a _collection_ of replicated pods running within a cluster.  

## Deployment  

A <font color="green">Deployment</font> defines the information pertaining to _how_ a pod should run: what function will it have (i.e. should there be a specific app running inside of it), how _many_ of the pods should be running, etc etc. This is typically laid out via [Helm](operating_systems/kubernetes/helm).  


# Installation  

Its difficult to pin down 'installing Kubernetes' - there are several platforms out there that do this: 
* Docker Desktop   
* Kind  
* K3s  
* [Minikube](/operating_systems/kubernetes/minikube)  

The one I have been introduced to is [Minikube](/operating_systems/kubernetes/minikube), so that is the one I will use. 

# Architecture  

There are 4 bare-minimum items that every node must run:  
* A Container Runtime  
   * This is usually [Docker](operating_systems/docker/) but it could be something else.  
* Kubeproxy  
* Kubelet  
* The Control Plane  


## Container Runtime  

The <font color="green">Container Runtime</font> is the architecture of the pods - in other words, _what_ are we running in the pods? As it turns out, this is typically [Docker](operating_systems/docker/), but there is a small chance it could be something else other than Docker.  

## Kubeproxy  

> More on <font color="green">Kubeproxy</font> [here](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/).  


The application <font color="green">Kubeproxy</font> proxies and load balances requests to the target pod(s). <font color="green">Kubeproxy</font> acts as _part_ of the Kubernetes' family of [services](operating_systems/kubernetes/kubernetes_basics?id=service).  

## Kubelet  

> More on <font color="green">Kubelet</font> [here](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/).  

The application <font color="green">Kubelet</font> interacts with the pods and the node itself.  <font color="green">Kubelet</font>:  
* Registers the node with the [kube-apiserver](operating_systems/kubernetes/kubernetes_basics?id=kube-apiserver).  
* Receives the description (i.e. [Helm Charts](operating_systems/kubernetes/helm)) on how to configure pods from the [master node](operating_systems/kubernetes/kubernetes_basics?id=master-node).  
* Ensures [pods](operating_systems/kubernetes/kubernetes_basics?id=pod) are running and healthy.  

## Control Plane  

The <font color="green">Control Plane</font> is a collection of apps that are required for Kubernetes to function; these apps run in pods on a [master node](operating_systems/kubernetes/kubernetes_basics?id=master-node). The <font color="green">Control Plane</font> consists of:  
* `kube-apiserver`  
* `kube-controller-manager`  
* `kube-scheduler`  
* `etcd`  


## kube-apiserver

> More on <font color="green">kube-apiserver</font> [here](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/).  

<font color="green">kube-apiserver</font> runs in a [pod](operating_systems/kubernetes/kubernetes_basics?id=pod) and is the front end for the Kubernetes API server. This allows the administrator to interact with the [cluster](operating_systems/kubernetes/kubernetes_basics?id=cluster) - without this, interacting with the cluster would be impossible.  


## kube-controller-manager  

> More on <font color="green">kube-controller-manager</font> [here](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/).  

<font color="green">kube-controller-manager</font> runs in a [pod](operating_systems/kubernetes/kubernetes_basics?id=pod) and is a daemon that manages the Kubernetes [controllers](operating_systems/kubernetes/kubernetes_basics?id=controller) - this allows administrators the ability to issue changes to targeted pod(s) on the fly.  

## kube-scheduler  

> More on <font color="green">kube-scheduler</font> [here](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/).  

<font color="green">kube-scheduler</font> runs in a [pod](operating_systems/kubernetes/kubernetes_basics?id=pod) and is a function that looks for newly created Pods that have no assigned nodes and then assigns them one.

## Etcd  

<font color="green">etcd</font> runs in a [pod](operating_systems/kubernetes/kubernetes_basics?id=pod) and is a key-value store which provides the backend database for Kubernetes;  <font color="green">etcd</font> stores and replicates the Kubernetes cluster state.  


# Pod Scaling  

## Set # of Instances  

To set a set number of instances, make sure [deployment.yaml](operating_systems/kubernetes/helm?id=deploymentyaml) contains the following snippet:  
```
spec:
  {{- if not .Values.autoscaling.enabled }}
  replicas: {{ .Values.replicaCount }}
  {{- end }}
```  
* This is set by default file when you [create a new Helm chart](operating_systems/kubernetes/helm?id=creating-new-helm-charts).  
* This depends on setting `replicaCount` in [values.yaml](operating_systems/kubernetes/helm?id=valuesyaml) (by default, this is set to `replicaCount: 1`, or '1 pod').  
  * Note that the enabled status of autoscaling is checked in the above code; if its turned on in [values.yaml](operating_systems/kubernetes/helm?id=valuesyaml); `replicaCount` will be valid.  


## Autoscaling  

> A good resource for this can be found on the [kubernetes.io page](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/); this is where I got most of this info. This page also gives a good example on how to test this out.  

Autoscaling can be achieved in Kubernetes with the correct [helm](operating_systems/kubernetes/helm) configuration.  

Firstly, the basic [hpa.yaml](operating_systems/kubernetes/helm?id=hpayaml) must be present (note that in my example I used the name `myNewHelmChart`, so you may need to replace that string). Next, the [values.yaml](operating_systems/kubernetes/helm?id=valuesyaml) file must have these set:

```
autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
  # targetMemoryUtilizationPercentage: 80
```  
* These settings are the default file when you [create a new Helm chart](operating_systems/kubernetes/helm?id=creating-new-helm-charts).  
* `enabled` is if autoscaling is enabled.  
* `minReplicas` is the minimum number of pods you want to run for this app.  
* `maxReplicas` is the maximum number of pods you want to run for this app.  
* `targetCPUUtilizationPercentage` is the target CPU utilization percentage you wish to set - so if this CPU Utilization is hit, another pod will be created.  
* `targetMemoryUtilizationPercentage` is the target memory utilization percentage you wish to set - so if this memory Utilization is hit, another pod will be created.  
   * Note this is commented out by default.  
