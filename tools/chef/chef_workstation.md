# Chef Workstation  

# Installation  

!> I will be showing how this is installed on Ubuntu - that said, you can also install this on other platforms (other Debian platforms, Red Hat, macOS, Windows, etc.).  Also, [linuxconfig.org](https://linuxconfig.org/how-to-install-chef-server-workstation-and-chef-client-on-ubuntu-18-04) and [techrepublic](https://www.techrepublic.com/article/how-to-install-the-chef-server-and-chef-client-on-ubuntu-20-04/) were helpful here.  

The <font color="green">Chef Workstation</font> is typically installed on your laptop / desktop.  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  

2\. Download the <font color="#FF8C00">.deb</font> file using one of these two methods:  
* Go to [downloads.chef.io](https://downloads.chef.io/). The Chef Workstation is [here](https://downloads.chef.io/tools/workstation).  
* Use wget 
  * You will need the address when using <font color="#FF8C00">wget</font> (see below).  
  * To actually get the file, run: `wget https://packages.chef.io/files/stable/chef-workstation/<CHEF WORKSTATION VERSION>/ubuntu/<UBUNTU VERSION>/chef-workstation_<CHEF WORKSTATION VERSION>-1_amd64.deb`
    * `<CHEF WORKSTATION VERSION>` is the version of Chef Workstation you wish to use; you can get the various Chef Workstation versions [here](https://downloads.chef.io/tools/workstation) (The current version is <font color="#FF8C00">21.10.640</font>).  
    * `<UBUNTU VERSION>` is your version of Ubuntu; right now the current Ubuntu version is <font color="#FF8C00">20.04</font> but <font color="#FF8C00">18.04</font> is also an option.  	
	* You can also change <font color="#FF8C00">ubuntu</font> or <font color="#FF8C00">amd64</font> if you wish (to get a different OS or architecture), but you will have to play with it.  
	  * An example is <font color="#FF8C00">debian</font>.  
	* A completed example: `wget https://packages.chef.io/files/stable/chef-workstation/21.10.640/ubuntu/20.04/chef-workstation_21.10.640-1_amd64.deb`  

3\. Install by running: `dpkg -i chef-workstation_*.deb`  

4\. Leave root: `exit`  
* From here on out I would not advize the use of root, unless you want to be root when you use Chef Workstation.  

5\. [Make a Chef Git repo](tools/chef/chef_workstation?id=chef-git-repo). Most examples use <font color="#FF8C00">chef-repo</font> as the repo name.  

6\. Create a `.chef` subdirectory in the [Chef Git repo](tools/chef/chef_workstation?id=chef-git-repo) you made in the step above. Assuming you have not entered the directory after you created it and assuming the name of the repo is <font color="#FF8C00">chef-repo</font>:  
```
cd chef-repo  
mkdir .chef
```  

7\. We need to get the [PEM file](tools/chef/chef_server?id=user-ssh-key) set up on the [Chef Server](tools/chef/chef_basics?id=chef-server). 
* This assumes you _have_ setup a [Chef Server](tools/chef/chef_server?id=installation) in a VM or other host and you have a root account and the IP of the host.  
* This assumes you have [created a user](tools/chef/chef_server?id=making-a-user) on that Chef server.
* If you have not done this at this point it's OK, but you _will_ have to do this eventually.  
* You can use [SSH](operating_systems/ubuntu/linux_notes?id=ssh) and [SCP](operating_systems/ubuntu/linux_notes?id=pushing-pulling-files-with-scp) to actually perform this action.  
  * Log into the Chef Server VM using [SSH](operating_systems/ubuntu/linux_notes?id=ssh).  
  * Locate the <font color="#FF8C00">.pem</font> file that was created when you [set-up the Chef Server](tools/chef/chef_server?id=installation).  You may have to copy it elsewhere then use [chown](operating_systems/ubuntu/linux_notes?id=changing-ownership-of-files) so your account owns it (and _not_ root).  
  * Exit the Chef Server so you are back on your local laptop / desktop; then, use [SCP](operating_systems/ubuntu/linux_notes?id=pushing-pulling-files-with-scp) to copy the <font color="#FF8C00">.pem</font> file into the <font color="#FF8C00">.chef</font> directory you created above on your local laptop / desktop.  
  * Do *not* forget to delete the _temporary_ <font color="#FF8C00">.pem</font> file you moved on the [Chef Server](tools/chef/chef_basics?id=chef-server), if you made a copy of the file in order to use [SCP](operating_systems/ubuntu/linux_notes?id=pushing-pulling-files-with-scp) to copy it to your laptop. Note you will _not_ delete the _original_ <font color="#FF8C00">.pem</font> file on the [Chef Server](tools/chef/chef_basics?id=chef-server) - this is only the copied version you created to move it off the Chef server.  

# Repo vs App vs Cookbook  

> When learning about Chef, I found that you could create a repo and also an 'app' but had no idea how they were different - [this article](https://devopsguru.tumblr.com/post/147717124737/chef-generate-app-vs-chef-generate-cookbook-vs#_=_) helped, as did [stackoverflow](https://stackoverflow.com/questions/28102653/whats-the-difference-between-chef-app-and-chef-repo#28104997) - so I will bundle the ideas of both here.  

In the early days of Chef, people would manually clone a GitHub project named `chef-repo`; _all_ cookbooks for all of your apps would be placed in here, and if you wanted another cookbook, you would use 

eventually, Chef introduced the [Chef Git repo](tools/chef/chef_workstation?id=chef-git-repo) command to do this as a shortcut.  have _one_ [Git](/learn_to_code/git/) repo that would contain _all_ of their companies [cookbooks](tools/chef/chef_basics?id=cookbook). 


# Chef Git Repo  

Chef utilizes [Git](/learn_to_code/git/) (it may be best to familiarize yourself with [Git](/learn_to_code/git/)). To make a Chef Git repo, navigate to a directory where you wold like to make the repo and then type:
```
chef generate repo REPO_NAME  
```  

`REPO_NAME` is the name of the Git repo - after you run the command, you will notice a directory with that same name is created. Enter that directory and you can then use [Git commands](learn_to_code/git/git_cli_commands).  

> Note there are _also_ 

# Chef Generate Cookbook  

To make a new cookbook, navigate to your repo directory and then run:  
```
chef generate cookbook COOKBOOK_NAME_HERE
```  
* Replace <font color="#FF8C00">COOKBOOK_NAME_HERE</font> with the name of your cookbook.  

