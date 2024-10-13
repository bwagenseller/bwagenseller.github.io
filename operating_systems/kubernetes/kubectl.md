# Kubectl  

# References  

## Top Tier References  

* The [Kubectl cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)  

## Other References  

* [Loft's Kubectl context examples](https://loft.sh/blog/kubectl-get-context-its-uses-and-how-to-get-started/)  

# Terminology  

## Context  

A single <font color="green">Context</font> represents a single Kubernetes cluster / environment; put another way, a single <font color="green">Context</font> represents a Kubernetes 'universe' that is independent from other universes.  

Practically speaking, you may have several independent Kubernetes clusters you may have to interact with - for example, you may have to issue Kubernetes commands to your 
* Development Environment  
* Staging Environment  
* Production Environment  
* Local Environment (for testing etc)  

Each of the above Kubernetes clusters are independent of each other; they each represent a unique <font color="green">Context</font>.  

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
kubectl describe pods
```  

The above describes all nodes (as none are specified).  To specify a specif entity, we can do that as well. For example, if we had a pod named `hello-minikube-77b6f68484-x5qwb` we could get information on that specific pod by:  
```
kubectl describe pods hello-minikube-77b6f68484-x5qwb
```  
## Describe Node  

> You can shorten `node` by just using `no` in all commands.  

To see all info about a node:  
```
kubectl describe node [NODE_NAME]
```  
* Do _not_ include the brackets `[]`.  
   * For all nodes, simply leave off the node name entirely.  

Some select fields that I like:  
* Name - shows the name of the node.  
* Roles - shows the role of the node.  
* Labels - lists all labels that are available to query via [the selector](operating_systems/kubernetes/kubectl?id=selector).  
* Addresses / InternalIP:  The IP of the node.  



* This is from the example on the [minikube site](https://minikube.sigs.k8s.io/docs/start/).  


## Describe Pods  

> You can shorten `pods` by just using `pod` or even `po` in all commands.  

## Describe Service  

## Describe Deployment  

> You can shorten `deployment` by just using `deploy` in all commands.  

## Describe ReplicaSet  

> You can shorten `replicaset` by just using `rs` in all commands.  

# Get  

## Get Node  

> You can shorten `node` by just using `no` in all commands.  

## Get Pods  

> You can shorten `pods` by just using `pod` or even `po` in all commands.  

To display all [pods](operating_systems/kubernetes/kubernetes_basics?id=pod):  
```
kubectl get pods  
```  
* If you wanted to see a _specific_ pod, you would simply include its name after the command.  
   * You can use a [selector](operating_systems/kubernetes/kubectl?id=selector) to get multiple pods with the same label, if desired.  
* Remember, you can use the [common flags](operating_systems/kubernetes/kubectl?id=common-flags) as well as teh [get-specific flags](operating_systems/kubernetes/kubectl?id=get-flags).  

## Get Services  

To display all running [services](operating_systems/kubernetes/kubernetes_basics?id=service):  
```
kubectl get service  
```  

## Get Deployment  

> You can shorten `deployment` by just using `deploy` in all commands.  

## Get ReplicaSet  

> You can shorten `replicaset` by just using `rs` in all commands.  

```
kubectl get replicaset
```  

## Get Flags  

In addition to the [common flags](operating_systems/kubernetes/kubectl?id=common-flags), `get` has some unique flags that can be used.  

**<font size="4">Output</font>**  

You can use an <font color="green">Output</font> flag to display a different output like so:
```
kubectl get pods --output wide  
```  
* In place of `--output` you could simply use `-o`  
* The above specified the 'wide' output, which would give more information
* There is also `--output name` which prints _just_ the name - this may be helpful for inputting into other commands later.  
* There are others as well - see `kubectl get --help` for those  

I like the `-o wide` option because:  
* For nodes, this adds both the internal and external IPs, the OS image, the kernel version, and the container-runtime.  
* For pods, this adds the IP of the pod as well as its associated node.   
* For deployments, this adds the containers, images, and selectors.   
* For replicasets, this adds the containers, images, and selectors.   
* For services....it just adds the selector. Meh.  




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


# Port Forward  

# Common Flags  

These common flags are _usually_ applicable to all `kubectl` commands (but not always).  

## Context Flag  

Its possible to run a kubectl command against a specific [context](operating_systems/kubernetes/kubectl?id=context) via the flag:  
```
 --context=[DESIRED_CONTEXT_NAME_HERE]  
```  
* `--context`  
   * Do _not_ include the brackets `[]`.  
* This is _only_ needed if you need to switch the context - otherwise, you can leave this off.  
   * You can [check the current context](operating_systems/kubernetes/kubectl?id=listing-showing-current-context), and if need be, you can [switch the context](operating_systems/kubernetes/kubectl?id=context-switching).  

## All Namespaces  

To display all [namespaces](operating_systems/kubernetes/kubernetes_basics?id=namespace), use the flag:  
```
--all-namespaces=true
```  
* This can be replaced by `-A`  
* By default, most pods are put in a `default` namespace and only these are shown with `get pods`
  * This allows you to see other namespaces, which is useful if you are running Kubernetes locally and you need to see the pods running that support Kubernetes.  

## Selector  

The selector flag can be used to identify an entity ([pod](operating_systems/kubernetes/kubernetes_basics?id=pod), [service](operating_systems/kubernetes/kubernetes_basics?id=service), etc) based off of a label. 

This can be advantageous, as sometimes you do not wish to look up the specific name (especially for pods) and / or you wish to return all pods with a single attribute as defined in a label. You can use a [describe](operating_systems/kubernetes/kubectl?id=describe) to find labels for entities.  

> Where is this label created? That can be complicated.  At least for [Helm charts](operating_systems/kubernetes/helm), this can be defined in the `_helpers.tpl` file.  

An implementation of the `--selector` flag is as follows:  
``` 
--selector app.kubernetes.io/name=some-element-name
```  
* You can replace `--selector` with `-l`  
* The label we are searching for above is `app.kubernetes.io/name`, and the value is `some-element-name`  
   * Note you have to be specific - you cannot just use `name`, you _must_ use the full label i.e. `app.kubernetes.io/name`  
   * It seems the format `app.kubernetes.io/XXXXX` is common for labels.  
* you can have multiple labels represented, just separate them with a comma i.e. `-l app.kubernetes.io/name=some-element-name,app.kubernetes.io/managed-by=Helm`  
 