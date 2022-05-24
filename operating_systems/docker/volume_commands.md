# Docker Volume Commands  

# Must Be Run As Root

This must be stated - almost all commands _must_ be run as root! There is a way to get around this, but there seems to be constant problems with it (the one I ran into revolved around [/var/run/docker.sock](https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue) which _requires_ you set this file's [chmod](operating_systems/ubuntu/linux_notes?id=changing-permissions) to `666` for every re-set, as well as [adding the user](operating_systems/ubuntu/linux_notes?id=adding-users-to-groups) to the `docker` group). Because of this, I run all commands as root.   

---  

# Mounting Drives  

Docker can [mount](operating_systems/ubuntu/linux_notes?id=mounting-unmounting-drives) directories from your host machine so they appear as a directory in your [container](operating_systems/docker/docker_basics?id=container). This page will address the approach to this using <font color="green">volumes</font>.  

# Bind Mounts vs Volumes - Which is Best?  

There are reasons to use a <font color="green">volume</font> instead of a [bound mount](operating_systems/docker/container_commands?id=bind-mounts) - particularly because you can set a mount point using a <font color="green">volume</font> in a Dockerfile but you _cannot_ set a mount point on the host directly in a Dockerfile. Also, you can use tools to search for a <font color="green">volume</font> but you may not be able to do that with a bound mount as easy (for example, to see all containers using a named <font color="green">volume</font>, you can simply type `docker container ls -af volume=VOLUME_NAME_HERE`).  

That said, Docker recommends the usage of a [bound mount](operating_systems/docker/container_commands?id=bind-mounts) over a <font color="green">volume</font>. This may be advisable if you dont have many containers running on the host / you do not have to keep track of too many mounted directories. If you only have a handful of containers on the host, I would simply use the [bound mount](operating_systems/docker/container_commands?id=bind-mounts); however, if I had to track _which_ containers are using _which_ mounts across several dozen containers, I would probably prefer the <font color="green">volume</font> approach due to the available tools for searching for volume use, etc.

# Creating a Mounted Volume  

Typically, Docker refers to these mounted drives as a <font color="green">volume</font>.  

The first step in creating a [mounted directory](operating_systems/ubuntu/linux_notes?id=mounting-unmounting-drives) via a <font color="green">volume</font> in Docker is by creating a <font color="green">volume</font> itself; a <font color="green">volume</font> is a collection of information about a mount point that can be used in Docker (name, absolute directory, etc). The largest downside of using a <font color="green">volume</font> is you cannot specify the location of the directory on the host.  

> Typically, when creating a <font color="green">volume</font>, Docker makes the directory on the local host something like `/var/lib/docker/volumes/YOUR_VOLUME_NAME_HERE/_data`.  

To create a <font color="green">volume</font>:  
```
docker volume create --name [VOLUME_NAME]  
```  
* Do _not_ include the brackets `[]`.  

# Mounting a Volume  

You can mount a volume using the `--volume` parameter:  
```
docker container run -dit --name mount_test -v /var/lib/docker/volumes/first_volume/_data:/opt/dir_in_container:rw ubuntu
```  
* The format is `-v DIRECTORY_ON_HOST:DIRECTORY_IN_CONTAINER`  
* The `:rw` means 'read / write' and is the default (you dont actually have to use this if you with for read / write permission on the mount).  
   * Again, you can make the above *read-only* by putting a `:ro` _directly_ at the end of the `DIRECTORY_IN_CONTAINER`, if you wish to do so.  

> You can make the above *read-only* by putting a `:ro` _directly_ at the end of the `DIRECTORY_IN_CONTAINER`.  

# List Volumes  

To list all <font color="green">volumes</font>:  
```
docker volume ls  
```  

# Inspect a Volume  

To show details of a particular volume:  
```
docker volume inspect [VOLUME_NAME]  
```  
* Do _not_ include the brackets `[]`.

This will show you something like the following:  
```
[
    {
        "CreatedAt": "2022-03-30T13:40:14-04:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/first_volume/_data",
        "Name": "first_volume",
        "Options": {},
        "Scope": "local"
    }
]
```  

# Deleting a Volume  

To delete a volume:  
```
docker volume rm [VOLUME_NAME]  
```  
* Do _not_ include the brackets `[]`.

!> In order to remove a volume, you _must_ make sure no containers are using the volume! You can check that out by [searching for volume usage](operating_systems/docker/volume_commands?id=searching-for-volume-usage), and if you find any containers connected to the volume, [stop](operating_systems/docker/container_commands?id=stop) the container and then [remove it](operating_systems/docker/container_commands?id=deleting-containers-rm).  


# Searching for Volume Usage  

> This is one of the reasons why someone would use a voume over simply using a [bound mount](operating_systems/docker/container_commands?id=bind-mounts) - you cannot do this with a bound mount.  

To see which containers are using a specific volume, you can use [ls](operating_systems/docker/container_commands?id=show-containers):  
```
docker container ls -af volume=[VOLUME_NAME_HERE]
```  
* Do _not_ include the brackets `[]`.  
