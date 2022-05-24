# Chef Server

# Installation  

> You must create either a VM (via You will be required to have at least 3GB of memory allocated to the server.  

!> I will be showing how this is installed on Ubuntu - that said, you can also install this on other platforms (other Debian platforms, Red Hat, macOS, Windows, etc.).  Also, [linuxconfig.org](https://linuxconfig.org/how-to-install-chef-server-workstation-and-chef-client-on-ubuntu-18-04) and [techrepublic](https://www.techrepublic.com/article/how-to-install-the-chef-server-and-chef-client-on-ubuntu-20-04/) were helpful here.  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  

2\. Download the <font color="#FF8C00">.deb</font> file using one of these two methods:  
* Go to [downloads.chef.io](https://downloads.chef.io/). The server is [here](https://downloads.chef.io/tools/infra-server).  
* Use wget 
  * You will need the address when using <font color="#FF8C00">wget</font> (see below).  
  * To actually get the file, run: `wget https://packages.chef.io/files/stable/chef-server/<CHEF SERVER VERSION>/ubuntu/<UBUNTU VERSION>/chef-server-core_<CHEF SERVER VERSION>-1_amd64.deb`
    * `<CHEF SERVER VERSION>` is the version of Chef Server you wish to use; you can get the various Chef server versions [here](https://downloads.chef.io/tools/infra-server) (The current version is <font color="#FF8C00">14.9.23</font>).  
    * `<UBUNTU VERSION>` is your version of Ubuntu; right now the current Ubuntu version is <font color="#FF8C00">20.04</font> but <font color="#FF8C00">18.04</font> is also an option.  	
	* You can also change <font color="#FF8C00">ubuntu</font> or <font color="#FF8C00">amd64</font> if you wish (to get a different OS or architecture), but you will have to play with it.  
	  * An example is <font color="#FF8C00">debian</font>.  
	* A completed example: `wget https://packages.chef.io/files/stable/chef-server/14.9.23/ubuntu/20.04/chef-server-core_14.9.23-1_amd64.deb`  

3\. Install by running: `dpkg -i chef-server-core_*.deb`  
* This may take awhile - it took at least 20 minutes for me.  

4\. To initialize (and initially start) the server, run: `chef-server-ctl reconfigure`  
* This may take a few minutes to run as well.  

5\. A directory _must_ be created so you can issue commands remotely via [ssh](operating_systems/ubuntu/linux_notes?id=ssh).  
* This directory will usually be created in root's home directory: `mkdir /root/.chef`  
* The [public key](operating_systems/ubuntu/linux_notes?id=more-on-the-ssh-lock-and-key) _must_ be converted to the <font color="purple">PEM</font> format, which is done [like so](operating_systems/ubuntu/linux_notes?id=creating-a-pem-file)). 
  * _HOWEVER_, when you make an account using the [user create](learn_to_code/chef/chef_server?id=making-a-user) command, these public and private keys are initially created for you, if you wish to use those.  

6\. Make a chef admin account using the [user create](learn_to_code/chef/chef_server?id=making-a-user) command.  

# Chef Server Command Overview  

The command <font color="#FF8C00">chef-server-ctl</font> controls many different subcommands on the [Chef server](learn_to_code/chef/chef_basics?id=chef-server). The [official documentation](https://docs.chef.io/server/ctl_chef_server/) lists all of the possible subcommands. I will not delve into all of these in these documents, but this will give you an idea of what is available.  

As a short overview of _all_ subcommands, <font color="#FF8C00">chef-server-ctl</font> can: 
* Backup / Restore Commands
  * <font color="#FF8C00">backup</font> - backs up all Chef data hosted in the server.  
  * <font color="#FF8C00">restore</font> - restores all Chef data hosted in the server from a previous backup.  
  * <font color="#FF8C00">cleanse</font> - basically restores all settings to default.  
* <font color="#FF8C00">gather-logs</font>  
* <font color="#FF8C00">help</font>  
* <font color="#FF8C00">install</font> - installs premium Chef features / add-ons.  
* Client [SSH key](operating_systems/ubuntu/linux_notes?id=ssh) commands
  * <font color="#FF8C00">add-client-key</font> - adds / alters a [client](learn_to_code/chef/chef_basics?id=node)'s [ssh key](operating_systems/ubuntu/linux_notes?id=ssh) in [PEM format](operating_systems/ubuntu/linux_notes?id=creating-a-pem-file)  
  * <font color="#FF8C00">delete-client-key</font>  
  * <font color="#FF8C00">list-client-keys</font> - Lists the associated keys to a [client](learn_to_code/chef/chef_basics?id=node).  
* Secret commands
  * <font color="#FF8C00">set-secret</font> - Allows you to store strings that should not be printed in files (passwords, etc).  
  * <font color="#FF8C00">remove-secret</font>  
  * <font color="#FF8C00">show-secret</font>  
* Credential / Secret Rotation  
  * <font color="#FF8C00">require-credential-rotation</font> - _All_ [SSH keys](operating_systems/ubuntu/linux_notes?id=ssh) need to be changed before Chef Server can run again.  
  * <font color="#FF8C00">rotate-shared-secrets</font> - Creates a new shared secret and salt, in addition to generating new service credentials.  
  * <font color="#FF8C00">rotate-all-credentials</font> - Generates new credential values for all service credentials.  
  * <font color="#FF8C00">rotate-credentials</font> - Like rotate-all-credentials but for a single service.  
* <font color="#FF8C00">cleanup-bifrost</font> - Removes unused authorization objects from the authorization database (called bifrost).
* Set Various Chef-related Passwords
  * <font color="#FF8C00">set-db-superuser-password</font> - sets the database password associated with Chef.  
  * <font color="#FF8C00">set-actions-password</font> - sets the RabbitMQ password.  
* Manage Organizations  
  * <font color="#FF8C00">org-create</font> - Creates an organization.  
  * <font color="#FF8C00">org-delete</font> - Deletes an organization.  
  * <font color="#FF8C00">org-list</font> - Lists all organizations.  
  * <font color="#FF8C00">org-show</font> - shows the details of a _specific_ organization.  
  * <font color="#FF8C00">org-user-add</font> - adds a user to an organization.  
  * <font color="#FF8C00">org-user-remove</font> - Removes a user from an organization.  
* User Commands  
  * <font color="#FF8C00">user-create</font> - Creates a new user.  
  * <font color="#FF8C00">password</font> - Changes a user's password.  
  * <font color="#FF8C00">user-list</font> - Lists all users.  
  * <font color="#FF8C00">user-show</font> - Shows the information of a specific user.  
  * <font color="#FF8C00">user-edit</font> - Edits the information of a specific user.  
  * <font color="#FF8C00">user-delete</font> - Removes a specific user.  
  * User [SSH key](operating_systems/ubuntu/linux_notes?id=ssh) commands  
    * <font color="#FF8C00">add-user-key</font> - Adds / alters a user's [ssh key](operating_systems/ubuntu/linux_notes?id=ssh) in [PEM format](operating_systems/ubuntu/linux_notes?id=creating-a-pem-file); doing so will allow a user to interact with the [Chef server](learn_to_code/chef/chef_basics?id=chef-server) via a [Chef Workstation](learn_to_code/chef/chef_basics?id=chef-workstation).  
    * <font color="#FF8C00">delete-user-key</font>  
    * <font color="#FF8C00">list-user-keys</font> - Lists a specific user's key  
* Database Subcommands  
  * <font color="#FF8C00">psql</font> - used to log into the PostgreSQL database associated with the given service.  
  * <font color="#FF8C00">reindex</font> - Used to reload Chef from PostgreSQL to Elasticsearch.  
* Configuration
  * <font color="#FF8C00">reconfigure</font> - _Must_ be run when Chef initially starts; also must be run after chef-server.rb is updated.  
  * <font color="#FF8C00">show-config</font> - View the configuration that will be generated by the <font color="#FF8C00">reconfigure</font> subcommand.
* Manage Administrators  
  * <font color="#FF8C00">grant-server-admin-permissions</font> - Grant a specific user server admin rights.  
  * <font color="#FF8C00">remove-server-admin-permissions</font> - Removes a specific user's server admin rights.  
  * <font color="#FF8C00">list-server-admins</font> - Lists the users that are in the 'server-admins' group.  
* <font color="#FF8C00">uninstall</font> - Remove the Chef Server application without removing the associated data.  
* <font color="#FF8C00">upgrade</font> - Upgrade the version of Chef server.  
* Service Subcommands  
  * <font color="#FF8C00">start</font> - Starts _all_ Chef services.  
  * <font color="#FF8C00">restart</font> - Restarts _all_ Chef services.  
  * <font color="#FF8C00">stop</font> - Stops _all_ Chef services.  
  * <font color="#FF8C00">status</font> - Gives the status of _all_ Chef services.  
  * <font color="#FF8C00">service-list</font> - Lists available services.  
  * <font color="#FF8C00">once</font> - Used for troubleshooting. Tells Chef Server to _not_ restart any service that fails.  
  * <font color="#FF8C00">hup</font> - Send a SIGHUP to all services.  
  * <font color="#FF8C00">int</font> - Send a SIGINT to all services.  
  * <font color="#FF8C00">kill</font> - Send a SIGKILL to all services.  
  * <font color="#FF8C00">term</font> - Send a SIGTERM to all services.  
  * <font color="#FF8C00">tail</font> - Follow all Chef Server logs for all services.
  

# User Commands  


## Making a User

To make a Chef user, perform these actions on the [Chef server](learn_to_code/chef/chef_basics?id=chef-server):  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  

2\. Run the following:
```
chef-server-ctl user-create USERNAME FIRSTNAME LASTNAME EMAIL 'PASSWORD' --filename ~/.chef/USERNAME.pem
```  
* `USERNAME` is the login of the user.  
* `FIRSTNAME` is the first name of the user.  
* `LASTNAME` is the last name of the user.  
* `EMAIL` is the email of the user.  
* `PASSWORD` is the password you want to set for this user.  
* You do not have to set the PEM file at first - it will be done here.  

## Showing, Editing, and Deleting User(s)  

To show all users: `chef-server-ctl user-list`  

To show a _single_ user: `chef-server-ctl user-show USERNAME`  
* `USERNAME` is the userID that was created for a given user a la the [user create](learn_to_code/chef/chef_server?id=making-a-user) command.  

To edit a user: `chef-server-ctl user-edit USERNAME`  
* `USERNAME` is the userID that was created for a given user a la the [user create](learn_to_code/chef/chef_server?id=making-a-user) command.  

## User SSH Key  

A [public key](operating_systems/ubuntu/linux_notes?id=more-on-the-ssh-lock-and-key) is necessary for interacting with the server; an initial one is set for a user if the [user create](learn_to_code/chef/chef_server?id=making-a-user) command was used to set-up their account, but it can be changed later if you wish.  

The [public key](operating_systems/ubuntu/linux_notes?id=more-on-the-ssh-lock-and-key) can be obtained if the command <font color="#FF8C00">chef-server-ctl user-show USERNAME</font> is issued, where <font color="#FF8C00">USERNAME</font> is the userID.  

The private key of all users will be stored in the <font color="#FF8C00">/root/.chef</font> directory (or whatever Chef directory you created in [the setup](learn_to_code/chef/chef_server?id=installation)); the format is <font color="#FF8C00">USERNAME.pem</font>, where <font color="#FF8C00">USERNAME</font> is the userID. The file is in [PEM](operating_systems/ubuntu/linux_notes?id=creating-a-pem-file) format.  


 