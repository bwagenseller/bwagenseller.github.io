# Chef Basics  

# What is Chef?  

Simply put, <font color="green">Chef</font> is an approach to automating DevOps / infrastructure tasks.  Its aim is to reduce errors introduced by humans running manual changes on hosts.  

# Chef Resources  

* The Chef [community website](https://www.chef.io/).  

# Chef Concepts  

## Chef Recipe  

A <font color="green">Chef Recipe</font> (or simply a <font color="green">Recipe</font>) is a descritpion of how a specific [node](tools/chef/chef_basics?id=node) should be configured. This can be things like (but not limited to):  
* Application feature files  
* Host files  
* Settings files  
* System configuration files 
* etc  

A <font color="green">recipe</font> can also be a set of commands that are to be executed on a specific [node](tools/chef/chef_basics?id=node).  

## Cookbook  

A <font color="green">Cookbook</font> is a collection of [recipes](tools/chef/chef_basics?id=chef-recipe).  

## Chef Server  

A <font color="green">Chef Server</font> is a server that both stores the [recipe](tools/chef/chef_basics?id=chef-recipe) and implements it on the relevant [nodes](tools/chef/chef_basics?id=node).  

For more on <font color="green">Chef Server</font>: 
* installation, [click here](tools/chef/chef_server?id=installation).  
* commands, [click here](tools/chef/chef_server?id=chef-server-command-overview).  
* a general over view of <font color="green">Chef Server</font>, [click here](tools/chef/chef_server).  

## Node  

A <font color="green">Node</font> is any other host that is managed by a <font color="purple">Chef</font> instance. For example, a <font color="green">node</font> can be a:  
* server  
* host  
* VM  
* hypervisor  
* etc  

## Chef Workstation  

A <font color="green">Chef Workstation</font> is any workstation (i.e. your laptop) that can update [recipes](tools/chef/chef_basics?id=chef-recipe) on a [Chef server](tools/chef/chef_basics?id=chef-server).  A <font color="green">chef Workstation</font> has [knife](tools/chef/chef_basics?id=knife) installed on it.  

## Knife

The <font color="green">Knife</font> application is installed on a [Chef workstation](tools/chef/chef_basics?id=chef-workstation), and its main purpose is to upload [cookbooks](tools/chef/chef_basics?id=cookbook) to [chef servers](tools/chef/chef_basics?id=chef-server).  

# Chef Versions  

# Chef Installation  

!> I will be showing how this is installed on Ubuntu - that said, you can also install this on other platforms (other Debian platforms, Red Hat, macOS, Windows, etc.).  Also, [linuxconfig.org](https://linuxconfig.org/how-to-install-chef-server-workstation-and-chef-client-on-ubuntu-18-04) and [techrepublic](https://www.techrepublic.com/article/how-to-install-the-chef-server-and-chef-client-on-ubuntu-20-04/) were helpful here.  

## Client Installation Basics  

Go to [downloads.chef.io](https://downloads.chef.io/)  
 * The client is [here](https://downloads.chef.io/tools/infra-client).  
