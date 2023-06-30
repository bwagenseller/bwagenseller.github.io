# Helm  

# What is Helm?  


Helm is a way to describe a [Kubernetes](operating_systems/kubernetes/) deployment. Helm manages Charts, and Charts are packages of pre-configured Kubernetes resources. It is written in [YAML](learn_to_code/yaml) using a [Go](https://go.dev/) template.  

# Installing Helm (Ubuntu)  

To install Helm:  
1\. Download your desired version from [Helm's Github page](https://github.com/helm/helm/releases).  

2\. Unpack: `tar -zxvf helm-v3.12.1-linux-amd64.tar.gz` 
* Obviously your file name may be different.  

3\. There are very few files in the zip file - find the one literally called `helm` and move it to the `/usr/local/bin` directory:  <br>`mv linux-amd64/helm /usr/local/bin/helm`
* You may want to do this as root.  

4\. Delete the downloaded file and temp directory that was created.  

# Helm Files and Directories  


## Base Directory (Maven)  

When used in a Java project that uses [Maven](learn_to_code/java/maven), Helm is typically located in a directory named `helm` located in the base directory (next to the [src directory](learn_to_code/java/maven?id=locations-of-files-in-maven)).  

## Chart.yaml  

The file <font color="green">Chart.yaml</font> is located in the `helm` directory and seems to be the 'master' file. 

An example:  
```
apiVersion: v2
name: mylibchart
description: A Helm chart for Kubernetes

# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application

# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
# Versions are expected to follow Semantic Versioning (https://semver.org/)
version: 0.1.0

# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application. Versions are not expected to
# follow Semantic Versioning. They should reflect the version the application is using.
# It is recommended to use it with quotes.
appVersion: "1.16.0"
```  
* Comments are made with a `#`.  
* `apiVersion` is the version of _Helm_ itself.  
* `appVersion` is the version of the app being managed by this chart (i.e. most likely the app you wrote or are maintaining in Java, C, etc etc).  
* `version` is the version of the chart, and should be updated when the app is updated.  




