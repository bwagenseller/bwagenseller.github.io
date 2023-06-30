# Kubectl  

# References  

## Top Tier References  

* The [Kubectl cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)  

## Other References  

* [Loft's Kubectl context examples](https://loft.sh/blog/kubectl-get-context-its-uses-and-how-to-get-started/)  

# Terminology  

## Context  

## Cluster  



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

# Get Pods  

To display all pods:  
```
kubectl get pods --context=[DESIRED_CONTEXT_NAME_HERE]  
```  
* `--context`  
   * Do _not_ include the brackets `[]`.  
   * This is _only_ needed if you need to switch the context - otherwise, you can leave this off.  
     * You can [check the current context](operating_systems/kubernetes/kubectl?id=listing-showing-current-context), and if need be, you can [switch the context](operating_systems/kubernetes/kubectl?id=context-switching).  