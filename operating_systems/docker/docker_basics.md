# Docker Basics 

# Before We Begin

## Useful Links

* [Docker Document Page](https://docs.docker.com)  


## Why I Do This

I find that I have to write things down, in a format I understand, in order for me to fully understand a topic; therefore I take notes on subjects I try to learn. These notes are an artifact of that line of thinking.

## My Note Sources

My main note sources are: 
* Various code examples I have seen on the web.  
* The `man` pages via Ubuntu Linux.  
   * `man docker-container-ls`  
   * `man docker-container`  
* [stackoverflow](https://stackoverflow.com/)  
* The book [Learn Docker in a Month of Lunches by Elton Stoneman](https://www.manning.com/books/learn-docker-in-a-month-of-lunches)  
* The Packt book [Docker for Developers  by Bullington-McGuire, Dennis, and Schwartz](https://subscription.packtpub.com/book/cloud_and_networking/9781789536058/1).  
* [Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)  
* [Docker Tutorial for Beginners (FULL COURSE in 3 Hours)](https://www.youtube.com/watch?v=3c-iBn73dDE)  

# Terms  

## Container  

> For information on container commands, see [here](operating_systems/docker/container_commands).  

A <font color="green">Container</font> that runs an application (or a small cluster of applications) in an environment similar to a virtual machine - it appears to have its own:
* Machine Name  
* IP Address  
* Disk Drive  

In essence, <font color="green">containers</font> have their own virtual environment; the applications running inside the container cannot interact with anything not in the container. Unlike a virtual machine, however, <font color="green">containers</font> are _not_ a fully isolated host - they _must_ share some same items with the host parent machine:
* CPU  
* Memory  
* Operating system  

A <font color="green">Container</font> is typically meant to run a simple task and stop; you _can_ have it run indefinitely (like an operating system), but many containers are simply designed to complete a task and then stop once that is done.  

## Docker Image (Term)  

> For information on image commands, see [here](operating_systems/docker/image_commands).  

A Docker <font color="green">image</font> is a bare-bones set of instructions to build a [container](operating_systems/docker/docker_basics?id=container) around a given idea; a Docker <font color="green">image</font> is sort of like an <font color="green">image</font> in a Virtual Machine, but there are some differences.  

An example of a Docker <font color="green">image</font> is an <font color="green">image</font> that represents the base Ubuntu operating system; it will have the bare necessities of OS instructions, tools, scripts, etc to run a container based off said Ubuntu <font color="green">image</font>.  

Often, you want to start off with a pre-built [container](operating_systems/docker/docker_basics?id=container) <font color="green">image</font> as a base, and from there you can build on, adding other commands, applications, etc. 

An <font color="green">image</font> can be used by multiple [containers](operating_systems/docker/docker_basics?id=container) at once (with each instance using the image as 'read only' with certain caveats); its important to note you cannot delete an image when a loaded container is tied to that image, as that would completely break that image.  

> To see some pre-built images, go [here](operating_systems/docker/docker_basics?id=locations-of-pre-built-images).  

## Dangling Image  

A <font color="green">dangling image</font> is an [intermediate image](operating_systems/docker/docker_basics?id=intermediate-image) image that is no longer used by / associated with a [container](operating_systems/docker/docker_basics?id=container).  

Typically, its best to [remove these images](operating_systems/docker/image_commands?id=remove-dangling-images) as they usually just take up space / cannot be used otherwise.  

## Intermediate Image  

An <font color="green">intermediate image</font> is an image that was used as a larger docker build at some point. An <font color="green">intermediate image</font> can be identified by a `<none>` in both the "Repository' and 'Tag' categories returned in a [image listing](operating_systems/docker/image_commands?id=listing-images).  

## Label  

A <font color="green">label</font> is a tag on a Docker entity ([container](operating_systems/docker/docker_basics?id=container), [image](operating_systems/docker/docker_basics?id=docker-image-term), etc) that adds some additional information we can use to identify the entity. For example, say you had multiple containers that acted as database hosts; you could give them all a 'database' label, so when searching through all of your containers you could concentrate on _just_ the database containers.  

Docker expects the label to be in a key / value combo; for example, using `-l type=database` is really storing a _key_ of 'type' and a _value_ of 'database'. You _can_ simply specify the key (with no '=' sign), and a _blank_ value will simply be assumed.  

Finally, its worth noting that Docker entities ([container](operating_systems/docker/docker_basics?id=container), [image](operating_systems/docker/docker_basics?id=docker-image-term), etc) can have _multiple_ labels - it is not restricted to just one!  

> For use of labels in various Docker entities, see:  
* [Container Labels](operating_systems/docker/container_commands?id=labels)

## Registry  

A <font color="green">registry</font> is a remote server that hosts image files.  

## Volume  

> For information on volume commands, see [here](operating_systems/docker/volume_commands).  

Its possible to make a specific directory on the host machine 'visible' to [container(s)](operating_systems/docker/docker_basics?id=container) on the host; the process is very similar to the traditional idea of a [mount](operating_systems/ubuntu/linux_notes?id=mounting-unmounting-drives).  

A <font color="green">volume</font> is a Docker entity that identifies the [mount](operating_systems/ubuntu/linux_notes?id=mounting-unmounting-drives) directory relationships between the host and its [containers](operating_systems/docker/docker_basics?id=container).  

# Why Use Docker  

## Density  

Docker is extremely skilled at levaraging <font color="green">Density</font>; <font color="green">Density</font> is the concept of running as many applications as possible on a host. Virtual machines are not particularly great for <font color="green">Density</font>, as a VM needs an _entire_ OS running for an application so its nowhere near as efficient as a Docker [container](operating_systems/docker/docker_basics?id=container).  

It may be argued that you could just pack more applciations into a VM and this would increase the <font color="green">density</font> of the VM; while this is true, its also true that many times, applications may need different versions of Java (or some other resource), tools that conflict with other tools, etc. Also, needless CPU time would be needed to run this OS, whereas that is not needed for a Docker [container](operating_systems/docker/docker_basics?id=container).  

## Isolation  

Docker is also good at <font color="green">Isolation</font>, which means running the target application will not impact any other application. This may not be a big deal at first glance, but in practice - applications may need to use different versions of tools, they may need to use the same ports, etc. <font color="green">Isolation</font> remedies this gracefully.  

# Installation 

## Docker Engine Installation (Ubuntu)  

> For the install, I followed the official install instructions [here](https://docs.docker.com/engine/install/ubuntu/) - but its condensed below.  

Unfortunately, Docker Desktop is not available on an LTS version of Ubuntu (currently) - we need to install <font color="green">Docker Engine</font> instead. To do so, follow these abridged instructions:  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  
2\. Update - run: `apt-get update`  
3\. Install various packages - run: `apt-get install ca-certificates curl gnupg lsb-release`  
4\. Add Docker’s official GPG key:  
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```  
5\. Set up the Docker repository - run:  
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```  
6\. Update the newly acquired Docker repository - run: `apt-get update`  
7\. Install <font color="green">Docker Engine</font> officially - run: `apt-get install docker-ce docker-ce-cli containerd.io`  
8\. Test to see if Docker installed properly - run: `docker version`; If you see (something like) this it was successful:  
```
Client: Docker Engine - Community
 Version:           20.10.13
 API version:       1.41
 Go version:        go1.16.15
 Git commit:        a224086
 Built:             Thu Mar 10 14:07:52 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.13
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.15
  Git commit:       906f57f
  Built:            Thu Mar 10 14:05:41 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.5.10
  GitCommit:        2a1d4dbdb2a1030dc5b01e96fb110a9d9f150ecc
 runc:
  Version:          1.0.3
  GitCommit:        v1.0.3-0-gf46b6ba
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

!> _All_ Docker commands _must_ be run as root!  

## Docker Compose Installation (Ubuntu) 

> Inspiration for the below instructions came from [here](https://www.cloudsigma.com/how-to-install-and-configure-docker-compose-on-ubuntu-20-04/).  

You will also need <font color="green">Docker Compose</font>; simply grab the binary from its github home. Here is how to do so.

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  
2\. Run: `curl -L "https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`  
* version 2.3.3 is currently the latest.  
* If you wish to use anything prior to 2.0.0, do _not_ use `...download/vX.X.X/docker-compose...` as above; remove the `v` in the version.  

3\. Change permissions on the file: `chmod +x /usr/local/bin/docker-compose`  
4\. Test: `docker-compose version`  
* If you see `Docker Compose version v2.3.3`, it worked!  
* If you see `/usr/local/bin/docker-compose: line 1: Not: command not found` it means the binary file did not download (you probably have the version wrong, or forgot to add / forgot to remove the 'v' near the version).  

# Uninstalling Docker  

To uninstall Docker:  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  

2\. Find all packages with the name docker in it via: `dpkg -l | grep -i docker`  
* The most common ones are:  
   * docker-engine
   * docker
   * docker.io
   * docker-ce
   * docker-ce-cli
   * docker-ce-rootless-extras
   * docker-scan-plugin  
   
3\. Run `apt-get purge -y` on the packages you found in step 2. This will take care of the above mentioned, but if you had more you will have to add them: `apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-ce-rootless-extras docker-scan-plugin`  

4\. Remove all related Docker directories: 
   * `rm -rf /var/lib/docker /etc/docker`  
   * `rm /etc/apparmor.d/docker`  
   * `rm -rf /var/run/docker.sock`  
   * `rm -rf /usr/local/bin/docker-compose`  
   * `rm -rf /etc/docker`  
   * `rm -rf ~/.docker`  
   
5\. Delete the Docker group: `groupdel docker`  

6\. Search for any running docker processes: `ps -ef | grep docker`  
* If you find any, note their [PID](operating_systems/ubuntu/linux_notes?id=process-handling) and kill them with `kill -9 PIDS_GO_HERE`  

# Re-Starting Docker  

To restart Docker itself:  
```
service docker restart
```  

# Locations of Pre-Built Images  

Pre-built images are stored in [registries](operating_systems/docker/docker_basics?id=registry), with the most famous one being [https://hub.docker.com/search?type=image](https://hub.docker.com/search?type=image).

You can find images [here](https://hub.docker.com/search?type=image), but my favorite are:
* [ubuntu](https://hub.docker.com/_/ubuntu)  
* [busybox](https://hub.docker.com/_/busybox)  
* [alpine](https://hub.docker.com/_/alpine)  
* [centos](https://hub.docker.com/_/centos)  
   
## Brents Favorite Images  

Here are some of my favorite images that are not standard, along with some brief notes on setup (if necessary).  

* [Grafana](https://hub.docker.com/r/grafana/grafana/tags) 
   * This one requires the [user account](operating_systems/docker/image_commands?id=full-image-identification) along with the repository name.  
* [Openjdk:8u252-jdk](https://hub.docker.com/layers/openjdk/library/openjdk/8u252-jdk/images/sha256-70342662a936e41adf4a224581b6185b5c120619f9ee9e6fabcedb4be3433251?context=explore)  
   * While openjdk is [here](https://hub.docker.com/_/openjdk?tab=tags), I wanted to showcase a [tag](operating_systems/docker/image_commands?id=identifying-images) (`:8u252-jdk` is the tag).  
* [quintoandar/docsify](https://hub.docker.com/r/quintoandar/docsify)  
   * This is [Docsify](learn_to_code/docsify/)  
   * There are many other Docsify containers in the repository, but they all seem either broken or hard to use.  
   * A basic way to create the container:  
```
docker run -dit --name docsify -v /PATH/TO/LOCAL/DIR:/docs:rw -p 3000:3000 quintoandar/docsify /bin/sh
```  
     * The name of this container will be `docsify` but you can change that if you wish.  
	 * The `/PATH/TO/LOCAL/DIR` is the path to the directory on your _local host_ that will house your [markdown files](learn_to_code/docsify/gettingstarted?id=files-needed).  
	 * The `-p 3000:3000` sets the [exposed and published ports](operating_systems/docker/container_commands?id=run-with-ports).  
	 * The `/bin/sh` overrides the default command; this is necessary, as the default command seems (currently) broken, and the container exits immediately.  
   * After you create the container, run the following commands:  
     * Run this to enter the container:  
```
docker container exec -it docsify /bin/sh	 
```  
       * You will now be in the container itself.  
     * Run this to initialize docsify (while in the container): `docsify init /docs`  
	 * Run this to start the docsify server (while in the container): `docsify serve /docs &`  
	   * The `&` forces the process into the background - if you do not do this, you will be required to leave the terminal up and running.  
	 * Exit the container: `exit`  
* [Wekan](https://hub.docker.com/r/wekanteam/wekan), a Kanban alternative.  

# Identifying Containers  

Every [container](operating_systems/docker/docker_basics?id=container) _must_ have a <font color="green">UUID</font> _and_ a <font color="green">name</font> associated with it. The <font color="green">UUID</font> is autogenerated when the container is made; it is a random alphanumeric string of 64 characters (the 64 character version is known as the <font color="green">UUID long identifier</font>, but you can reference it with the first 12 characters, which is known as the <font color="green">UUID short identifier</font>).  

You also get to specify the name (if you don't, the name is is also auto-generated); you can explicitly specify the name when you [initially create the container](operating_systems/docker/container_commands?id=naming-a-container).  

# Port Basics  

> I learned this mostly from [Baeldung](https://www.baeldung.com/ops/docker/expose-vs-publish).  

To work with ports in Docker, you need to understand what an <font color="green">Exposed Port</font> is, as well as a <font color="green">Published Port</font>. 

An <font color="green">Exposed Port</font> references an _internal_ port in the running Docker [container](operating_systems/docker/docker_basics?id=container) itself, whereas a <font color="green">Published Port</font> references a port _on the host machine running Docker_ that will act as a passthrough from the host to the container.

For example, if you run an instance of `nginx` like so:

```
docker run -dit -p 8080:80 nginx
```  

This will run a new instance of `nginx`, with a <font color="green">Published Port</font> of 8080 and an <font color="green">Exposed Port</font> of 80. The external world can interact with the container via the host's port 8080. For example, if you run the above and then navigate to http://127.0.0.1:8080/ in your browser (on the host), the request will go from your local host, through its port 8080, to the container through its port 80, and from there it will interact with nginx which is running in the container.
