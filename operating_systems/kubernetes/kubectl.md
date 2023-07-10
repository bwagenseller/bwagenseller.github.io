# Kubectl  

# References  

## Top Tier References  

* The [Kubectl cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)  

## Other References  

* [Loft's Kubectl context examples](https://loft.sh/blog/kubectl-get-context-its-uses-and-how-to-get-started/)  

# Terminology  

## Context  

# Setup  

## Install 

> These instructions came from [the Kubernetes website](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).  

To install Kubectl:  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  

2\. Download the file: `curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"`  

3\. Install: `install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl`  

4\. Check the version: `kubectl version --client --output=yaml`  

> You could install this the traditional way via the native package management command `apt`, but you will need to install additional packages and add the Kubernetes apt repository; see [here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management).  


# Config  

## Config File  

The Kubectl config file is located at `~/.kube/config`. 

## Showing the Config  

To show the config setup, type:  
```
kubectl config view
```  

## Listing / Showing Current Context  

Every Kubectl command _must_ have a [context](operating_systems/kubernetes/kubectl?id=context); fortunately, you do not have to set this every time, but you can [switch your context]() if necessary.  

That said, to list all contexts:  
```
kubectl config get-contexts
```  
* You can switch the context [like so](operating_systems/kubernetes/kubectl?id=context-switching), if necessary.  

To simply see your current context:  
```
kubectl config current-context
```  

## Context Switching  

Every Kubectl command _must_ have a [context](operating_systems/kubernetes/kubectl?id=context); therefore, the concept of <font color="green">Context Switching</font> becomes critical if you have multiple contexts associated with Kubectl.  

Typically, Kubectl use the current context ([here](operating_systems/kubernetes/kubectl?id=listing-showing-current-context) is how you see the current context); if this is not the context you want, you can switch the context like so:  
```
kubectl config use-context [DESIRED_CONTEXT_NAME_HERE]  
```  
* Do _not_ include the brackets `[]`.  

---  

# Describe  

You can see a great deal of information about a node, deployment, service, or pod with a Describe. For example, you can describe all pods (or a single pod) with:  
```
kubectl describe node
```  

The above describes all nodes (as none are specified).  To specify a specif entity, we can do that as well. For example, if we had a pod named `hello-minikube-77b6f68484-x5qwb` we could get information on that specific pod by:  
```
kubectl describe pods hello-minikube-77b6f68484-x5qwb
```  

# Get Pods  

To display all [pods](operating_systems/kubernetes/kubernetes_basics?id=pod):  
```
kubectl get pods --context=[DESIRED_CONTEXT_NAME_HERE]  
```  
* `--context`  
   * Do _not_ include the brackets `[]`.  
   * This is _only_ needed if you need to switch the context - otherwise, you can leave this off.  
     * You can [check the current context](operating_systems/kubernetes/kubectl?id=listing-showing-current-context), and if need be, you can [switch the context](operating_systems/kubernetes/kubectl?id=context-switching).  
* `--all-namespaces=true`  
   * This can be replaced by `-A`  
   * By default, most pods are put in a `default` namespace and only these are shown with `get pods`
     * This allows you to see other namespaces, which is useful if you are running Kubernetes locally and you need to see the pods running that support Kubernetes.  

# Show Deployments 

To display all running [deployments](operating_systems/kubernetes/kubernetes_basics?id=deployment):  
```
kubectl get deployment
```  

With minikube running, this displays:  
```
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
hello-minikube   1/1     1            1           2d18h
```


# Create Deployment  

You can create a simple [deployment](operating_systems/kubernetes/kubernetes_basics?id=deployment) like so:  
```
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0  
```  
* This is from the example on the [minikube site](https://minikube.sigs.k8s.io/docs/start/).  
   * The specific image `kicbase/echo-server:1.0` is located [here](https://hub.docker.com/r/kicbase/echo-server).  
* The name is `hello-minikube`, but you should change this to whatever you wish.  
* The `--image` specifies a [Docker image](operating_systems/docker/image_commands).  
   * The example above has an image pre-defined so we do not have to do much more to it - that will probably not be the case when you do this 'for real'!  

# Expose Deployment  

# Stop Deployment  

> A driving force behind Kubernetes is services should never be offline - its important to have whatever you have running at all times. This ideology makes the idea of a 'stop' a bit archaic, and thus the word 'stop', 'halt', quit' etc are not really in the vocabulary of kubectl (although 'delete' is). That said, there are times where stopping - but not deleting - a service is important, so this is a trick to performing a stop.  

According to [yourdevopsmonitor](https://yourdevopsmentor.com/blog/how-to-stop-all-kubernetes-deployments/), you cannot _stop_ a deployment; that said, you _can_ scale the replicas to 0, effectively stopping it.  

In order to stop a deployment, you must set the number of replicas to 0. To do that:  
```
kubectl scale deployment hello-minikube --replicas 0
```  
* This uses the example on the [minikube site](https://minikube.sigs.k8s.io/docs/start/).  
* The name is `hello-minikube`, but you should change this to whatever you wish.  

# Get Services  

To display all running [services](operating_systems/kubernetes/kubernetes_basics?id=service):  
```
kubectl get service  
```  

# Port Forward  