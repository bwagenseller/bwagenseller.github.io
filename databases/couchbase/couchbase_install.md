# Couchbase Install

# Installing the SDK

The SDK (software development kit) is necessary for machines interacting with the server - so install this on machines that will be running Java or Python scripts that will interact with couchbase.  

## Installing Couchbase SDK (Ubuntu) 

> These instructions were originally found [here](https://docs.couchbase.com/c-sdk/2.10/start-using-sdk.html).  

To use the Couchbase software development kit (for Python, Java, etc), you must first install some keys; after that, you can run some `apt-get` commands to install the SDK. 

To get the keys (as root):
```
wget http://packages.couchbase.com/releases/couchbase-release/couchbase-release-1.0-6-amd64.deb
dpkg -i couchbase-release-1.0-6-amd64.deb
```  

Next, simply update and then install the libraries:  
```
apt-get update
apt-get install libcouchbase-dev libcouchbase2-bin build-essential
```

## Installing Python SDK Library

Once you have the [couchbase SDK libraries installed](databases/couchbase/couchbase_install?id=installing-couchbase-sdk-ubuntu) run:
```
pip install --upgrade couchbase
```  

# Installing Couchbase


