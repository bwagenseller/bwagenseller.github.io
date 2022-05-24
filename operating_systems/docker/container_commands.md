# Docker Container Commands  

# Must Be Run As Root

This must be stated - almost all commands _must_ be run as root! There is a way to get around this, but there seems to be constant problems with it (the one I ran into revolved around [/var/run/docker.sock](https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue) which _requires_ you set this file's [chmod](operating_systems/ubuntu/linux_notes?id=changing-permissions) to `666` for every re-set, as well as [adding the user](operating_systems/ubuntu/linux_notes?id=adding-users-to-groups) to the `docker` group). Because of this, I run all commands as root.   

--- 

# Naming a Container  

Every [container](operating_systems/docker/docker_basics?id=container) _must_ have a [name](operating_systems/docker/docker_basics?id=identifying-containers) associated with it. The name is autogenerated when the container is made, unless you opt to specify the name.  

## Naming Containers at Creation  

!> This section mentions "running" a container - for more info on that, see [running containers](operating_systems/docker/container_commands?id=running-containers).  

If you want to explicitly specify the name, use the `--name` flag when you initially create the container:  
```
docker run -dit --name my-docker-image-name ubuntu
```  
* This names the container `my-docker-image-name` with the `--name` flag.  
* You will read about the `-d`, `-i`, and `-t` flags in the section on [running containers](operating_systems/docker/container_commands?id=running-containers).  

---  

# Labels  

## Labels on Container Startup

!> This section mentions "running" a [container](operating_systems/docker/docker_basics?id=container) - for more info on that, see [running containers](operating_systems/docker/container_commands?id=running-containers).  

To attach a label to a new container when you first run it, you can use the `--label` (or `-l`) parameter like so when initally running the container:  
```
docker run -dit -l "type=database" ubuntu
```  
* This starts a new container with a label using a key of 'type' and a value of 'database'.  
* The double quotes are not needed if there are no spaces.  
* Again, you do not need the value; you could simply say `-l "database"`; this will set a key of 'database' and a blank value.  
* You will read about the `-d`, `-i`, and `-t` flags in the section on [running containers](operating_systems/docker/container_commands?id=running-containers).  

## Searching for Labels 

> You can learn more about viewing containers - as well as the `-a` flag - [here](operating_systems/docker/container_commands?id=show-all-containers).  

To search for all [containers](operating_systems/docker/docker_basics?id=container) with a given label, you can use a [filter](operating_systems/docker/container_commands?id=filtering-listed-containers) like so:  
```
docker container ls -af label="type=database"
```  
* This searches for all containers with a label that has a key of "type" and a value of 'database'.  
* You could also simply use `label="type"` if you wanted _all_ labels with a "type" as a key.  
* Searches are _exact_ searches - so far, I have not found a wildcard that can be used, and partially typing the key - or value - does not work either.  

For me, this returns:  
```
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                     NAMES
20fb3b2f998d   ubuntu    "bash"                   5 seconds ago    Up 3 seconds                                              amazing_ganguly
ce67af247cf2   ubuntu    "bash"                   34 minutes ago   Up 34 minutes                                             naughty_curie
de6e78aeed55   nginx     "/docker-entrypoint.…"   35 minutes ago   Up 35 minutes   0.0.0.0:49154->80/tcp, :::49154->80/tcp   silly_ishizaka
```  
* Note the label is not naturally displayed with a simple `ls -f label=` - you will need to use the `--format` parameter to show the labels.

--- 

# Running Containers  

The basic command to **build and run** a [container](operating_systems/docker/docker_basics?id=container) is:  
```
docker container [IMAGE_NAME]:[TAG] [COMMAND]
```  
* Do _not_ include the brackets `[]`.  
* The `IMAGE_NAME` is an [image](operating_systems/docker/docker_basics?id=docker-image-term); you can check out some pre-built images [here](operating_systems/docker/docker_basics?id=locations-of-pre-built-images).  
* The `:TAG` is not required; however, if you want a _specific_ version of the image this is how you specify it. More on tags [here](operating_systems/docker/image_commands?id=identifying-images).  
   * If you omit the `:TAG`, the image pulled will be the default image for the given `IMAGE_NAME` (usually its the tag `latest`).  
* You could also pull the image using the [digest](operating_systems/docker/image_commands?id=digest).  
* `[COMMAND]` is optional - every image has exactly [one command that is run when the container starts](operating_systems/docker/dockerfile?id=command-run-on-start), but that can be overridden if you plave the `[COMMAND]` here.  
   * This is not usually done.  
   * This can be a shell script inside the container itself.  

Most commands build off this.  

!> Note that <font color="green">run</font> is only used for _initially_ starting / creating a container; if you wish to re-start a container that was previously running, use [start](operating_systems/docker/container_commands?id=start) instead.  

## Run Interactively  

The command `docker container run` (shortened: `docker run`) is the basic command to run a docker container. You need to specify an [image](operating_systems/docker/docker_basics?id=locations-of-pre-built-images) (I like to use the ubuntu image for testing). To run a basic Ubuntu container:
```
docker container run --interactive --tty ubuntu
```

If you do not include the `--interactive` you cannot connect to the container's stdin/stdout (so you will not be able to actually send any commands); if you do not include `--tty` you will not be able to connect to a terminal; if you want to interact at all, you need _both_; that said, the flag `-it` covers both:  
```
docker container run -it ubuntu
```  
* Again, you can add a `:[TAG]` after `ubuntu` (no space) to specify a specific version of Ubuntu; its not required, but if you want a _specific_ version of the image this is how you specify it. More on tags [here](operating_systems/docker/image_commands?id=identifying-images).  
   * If you omit the `:TAG`, the image pulled will be the default image for the given `IMAGE_NAME` (usually its the tag `latest`).  
* You could also pull the image using the [digest](operating_systems/docker/image_commands?id=digest).  

!> Note that <font color="green">run</font> is only used for _initially_ starting / creating a container; if you wish to re-start a container that was previously running, use [start](operating_systems/docker/container_commands?id=start) instead.  

## Run In Background  


Building off [running interactively immediately](operating_systems/docker/container_commands?id=run-interactively), you can run in the background with:  
```
docker run -dit ubuntu
```  
* This uses the pre-built Ubuntu [image](operating_systems/docker/docker_basics?id=locations-of-pre-built-images).  
* The 'detach' option `-d` (or `--detach`) runs the [container](operating_systems/docker/docker_basics?id=container) in the background and allows for future connections to it via [exec](operating_systems/docker/container_commands?id=enter-running-container) later on.  
* The `-it` flags (mentioned [here](operating_systems/docker/container_commands?id=run-interactively)) are still important.  
* Again, you can add a `:[TAG]` after `ubuntu` (no space) to specify a specific version of Ubuntu; its not required, but if you want a _specific_ version of the image this is how you specify it. More on tags [here](operating_systems/docker/image_commands?id=identifying-images).  

!> Note that <font color="green">run</font> is only used for _initially_ starting / creating a container; if you wish to re-start a container that was previously running, use [start](operating_systems/docker/container_commands?id=start) instead.  

## Adding Environment Variables  

You can add environment variables on the `run` command with:  
```
docker run -dit --env Animal1=Turtle --env Animal2=Frog ubuntu
```  
* This uses the pre-built Ubuntu [image](operating_systems/docker/docker_basics?id=locations-of-pre-built-images), but you can use what you want.  
* The `--env` parameter sets up the environment variable.  
   * What follows is a key/value pair, with the environment variable being the key and its value the value.  
   * The example above sets two separate environment variables - Animal1 and Animal2.  
   
The number of environment variables set is an arbitrary number.  

## Run With Ports  

> See [here](operating_systems/docker/docker_basics?id=port-basics) for a description of port basics as well as a definition of <font color="green">Exposed Ports</font> and <font color="green">Published Ports</font>.  

[Ports](operating_systems/docker/docker_basics?id=port-basics) are critical in Docker. To show a basic example that relies on ports, we will use the `nginx` image and set the <font color="green">Exposed Port</font>:<font color="green">Published Port</font> to `8080:80` like so:  
```
docker run -dit -p 8080:80 nginx
``` 
* To see an explanation of the above, see [here](operating_systems/docker/docker_basics?id=port-basics).  
* The flag `-p` is short for `--publish`  

If you were to [show the basics](operating_systems/docker/container_commands?id=show-all-containers) of the running [container](operating_systems/docker/docker_basics?id=container) by running `docker container ls -a` you may see someting like this:
```
CONTAINER ID   IMAGE    COMMAND                  CREATED             STATUS                     PORTS                                   NAMES
5f7f4fcad376   nginx    "/docker-entrypoint.…"   11 minutes ago      Up 11 minutes              0.0.0.0:8080->80/tcp, :::8080->80/tcp   jovial_khayyam
```  

Notice the ports list `0.0.0.0:8080->80/tcp`; this `0.0.0.0` means that accessing `localhost:8080` in a browser across _any_ interface on the host will be able to access your container. If you _just_ wanted to limit it to, say, `127.0.0.1` you could do that like so:  
```
docker run -dit -p 127.0.0.1:8080:80 nginx
```  

Sometimes, you may not know what ports are expressly <font color="green">exposed</font> in the image; if you simply want to <font color="green">publish</font> all <font color="green">exposed</font> ports you can do so with `--publish-all` like so:  
```
docker run -dit --publish-all nginx
```  

I ran the above and it assigned a random host port of `49153` as the <font color="green">published port</font>. Running a `docker container ls -a` gives me:  
```
CONTAINER ID   IMAGE    COMMAND                  CREATED         STATUS            PORTS                                     NAMES
0c2fa71af02d   nginx    "/docker-entrypoint.…"   3 seconds ago   Up 2 seconds      0.0.0.0:49153->80/tcp, :::49153->80/tcp   wizardly_lovelace
```  

## Running With a Network  

Its possible to connect the container to a specific [Docker Network](operating_systems/docker/networking). First, [get the identifier of the network](operating_systems/docker/networking?id=showing-networks), and once you have that, connect to it like so:  
```
docker run -dit --name my-docker-image-name --network nat ubuntu
```  
* This names the container `my-docker-image-name` with the `--name` flag.  
* The flag `--network` indicates the network we want to connect this container to (in our case, its the `nat` network, which we have [created at another time](operating_systems/docker/networking?id=creating-a-network).  

---  

# Enter A Container - Attach  

In addition to [using exec to enter a running container](operating_systems/docker/container_commands?id=enter-running-container), you can also use the `attach` command like so:  
```
docker attach [CONTAINER_NAME]
```  
* Do _not_ include the brackets `[]`.  

!> Note that when you go to exit the [container](operating_systems/docker/docker_basics?id=container), you _cannot_ use `exit` if you want the container to continue to run - using `exit` also shuts down the container!  Using [exec to enter a running container](operating_systems/docker/container_commands?id=enter-running-container) does _not_ have this problem, so I prefer that method.  

---  

# Start  

To re-start a [previously run container](operating_systems/docker/container_commands?id=running-containers) which is now [stopped](operating_systems/docker/container_commands?id=stop), you can use the <font color="green">start</font> command. Example:  
```
docker start [CONTAINER_NAME]
```  
* Do _not_ include the brackets `[]`.  
* The name comes from the name [you initially assigned to the container](operating_systems/docker/container_commands?id=naming-a-container).  

You can use the `-i` flag to display any output the container prints to the screen like so:  
```
docker start -i [CONTAINER_NAME]
```  


---  

# Stop  

To stop a running [container](operating_systems/docker/docker_basics?id=container), you must reference it by [name or short UUID](operating_systems/docker/container_commands?id=naming-a-container):  
```
docker stop [CONTAINER_NAME]
```  
* Do _not_ include the brackets `[]`.  
* This can also be the [long or short UUID](operating_systems/docker/container_commands?id=naming-a-container).  

---  

# Show Containers  

You can see all running [containers](operating_systems/docker/docker_basics?id=container) with:
```
docker containers ls
```

Which will show something like this:  
```
CONTAINER ID   IMAGE         COMMAND     CREATED        STATUS        PORTS     NAMES
a7247db71f9d   diamol/base   "/bin/sh"   27 hours ago   Up 27 hours             jovial_keldysh
```  
* What is shown:
   * The [short UUID](operating_systems/docker/container_commands?id=naming-a-container).
   * The [image](operating_systems/docker/docker_basics?id=docker-image-term).  
   * The shell (or other app) its running.
   * The create time.  
   * The status.
   * The ports its using.  
   * Its [name](operating_systems/docker/container_commands?id=naming-a-container).  
* You can substitute `ps` for `ls` as it seems to be the same.  

## Show All Containers  

To show all [containers](operating_systems/docker/docker_basics?id=container) - _not_ just the active ones - use `-a`:
```
docker container ls -a
```  

It will print something like:
```
CONTAINER ID   IMAGE                      COMMAND                 CREATED        STATUS                      PORTS     NAMES
feab4d15a9f7   centos                     "/bin/bash"             20 hours ago   Exited (1) 20 hours ago               pensive_gagarin
a50e05845b29   busybox                    "sh"                    20 hours ago   Exited (0) 20 hours ago               dreamy_robinson
9a76706d4b41   busybox                    "sh"                    21 hours ago   Exited (137) 17 hours ago             competent_haibt
ff1190e6c84f   busybox                    "sh"                    21 hours ago   Exited (0) 21 hours ago               hopeful_sinoussi
f99d3e87bba9   python                     "python3"               21 hours ago   Exited (0) 21 hours ago               pedantic_ishizaka
a843a39f5097   python                     "python3"               21 hours ago   Exited (0) 21 hours ago               inspiring_moore
a7247db71f9d   ubuntu                     "/bin/sh"               43 hours ago   Up 43 hours                           jovial_keldysh
```  

## Show Only Short UUIDs  

Sometimes its best to get a list of identifiers quickly - to do that, use `-q`: 
```
docker container ls -q
```  

## Filtering Listed Containers  

Its possible to filter returned [containers](operating_systems/docker/docker_basics?id=container) with the `--filter` (or `-f`) parameter. Every filter needs _at least_ one condition listed immediately after the flag; the possible conditions are:  

| Condition | Values | Description | Example |  
| --- | --- | --- | --- |  
| ancestor | | Usually, this will list all containers built off the given [image](operating_systems/docker/docker_basics?id=docker-image-term) name; however, things can get weird if your container is built off of other containers (see [here](https://stackoverflow.com/questions/37255264/why-does-docker-filter-ancestor-imagename-find-the-wrong-container), which lists ways to get around this). | `docker container ls -af ancestor=ubuntu` |  
| before | | Shows a list of containers _created_ (not necessarily started) before the listed container. | `docker container ls -af before=[CONTAINER_NAME]` |  
| expose | | Shows all containers with the listed [exposed ports](operating_systems/docker/docker_basics?id=port-basics). | See [here](operating_systems/docker/container_commands?id=searching-by-exposed-ports). |  
| exited | | Shows all containers that are in the `exited` status _and_ have the numerical exit code as indicated. | `docker container ls -af exited=137` |  
| health | (starting, healthy, unhealthy, none) | Checks the health of the container; I believe you must [set up a health check](https://www.techrunnr.com/how-to-add-health-check-for-docker-containers/) for this to work properly (if you do not, the health of the container will always be `none`). | `docker container ls -af health=unhealthy` |  
| id | | Serch using the container's [short UUID](operating_systems/docker/docker_basics?id=identifying-containers) | `docker container ls -af id=[CONTAINER_ID]` |  
| isolation | (default, process, hyperv) | (<font color="red">Note</font>: Windows daemon only) | |  
| is-task | (true, false) | Lists containers that are officially a 'task' for another service. | `docker container ls -af is-task=false` |  
| label | | Search for a specific label (either they key or both key/value) across the given containers. | See [here](operating_systems/docker/container_commands?id=searching-for-labels). |  
| name | | Search using a specific container's name. | See [here](operating_systems/docker/container_commands?id=searching-by-container-name). |  
| network | | Search for all containers connected to a specific network (according to Docker); can either be the ID or the network name. | `docker container ls -af network=myNetwork` |  
| publish | | Shows all containers with the listed [published ports](operating_systems/docker/docker_basics?id=port-basics). | See [here](operating_systems/docker/container_commands?id=searching-by-published-ports). |  
| since | | Shows a list of containers _created_ (not necessarily started) after the listed container. | `docker container ls -af since=[CONTAINER_NAME]` |  
| status | (created, restarting, removing, running, paused, exited) | Shows a list of containers based off one of the lised statuses.| `docker container ls -af status=running` |  
| volume | | Show which containers are using a mounted [volume](operating_systems/docker/volume_commands?id=creating-a-mounted-volume). | See [here](operating_systems/docker/volume_commands?id=searching-for-volume-usage). |  
* Taken from the `docker-container-ls` man page.  
* If you see `[CONTAINER_NAME]` above:  
   * Do _not_ include the brackets `[]`.  
   * The name comes from the name [you initially assigned to the container](operating_systems/docker/container_commands?id=naming-a-container).  
* If you see `[CONTAINER_ID]` above:  
   * Do _not_ include the brackets `[]`.  
   * The ID is the [short UUID](operating_systems/docker/docker_basics?id=identifying-containers).  
   
I will be running some examples of `filter`, and it will be good to show _all_ of my containers for reference:  
```
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS                     PORTS                                     NAMES
20fb3b2f998d   ubuntu                     "bash"                   5 seconds ago   Up 3 seconds                                                         amazing_ganguly
ce67af247cf2   ubuntu                     "bash"                   4 seconds ago   Up 3 seconds                                                         naughty_curie
0c2fa71af02d   nginx                      "/docker-entrypoint.…"   3 seconds ago   Up 2 seconds               0.0.0.0:49153->80/tcp, :::49153->80/tcp   wizardly_lovelace
5f7f4fcad376   nginx                      "/docker-entrypoint.…"   6 hours ago     Up 6 hours                 0.0.0.0:8080->80/tcp, :::8080->80/tcp     jovial_khayyam
9238ab2c314f   nginx                      "/docker-entrypoint.…"   6 hours ago     Up 6 hours                 80/tcp                                    elastic_lichterman
e3facae8939f   busybox                    "sh"                     7 hours ago     Up 7 hours                                                           bold_dewdney
9f51eeae163c   ubuntu                     "bash"                   10 days ago     Exited (0) 10 days ago                                               my-brent
596f3956b190   ubuntu                     "bash"                   10 days ago     Exited (0) 10 days ago                                               myBrent
eff8c945d836   ubuntu                     "bash"                   10 days ago     Exited (0) 10 days ago                                               peaceful_germain
5b86db8b055c   ubuntu                     "bash"                   10 days ago     Exited (127) 10 days ago                                             nervous_cori
663dfee9508d   ubuntu                     "bash"                   10 days ago     Exited (127) 10 days ago                                             bold_blackwell
a1b33ce3e8d1   ubuntu                     "bash"                   10 days ago     Exited (0) 10 days ago                                               crazy_engelbart
17f71b0d1796   ubuntu                     "bash"                   10 days ago     Exited (129) 10 days ago                                             charming_hypatia
53faf32e3448   ubuntu                     "bash"                   10 days ago     Exited (0) 10 days ago                                               angry_jackson
e16ba2813da6   ubuntu                     "bash"                   10 days ago     Exited (129) 10 days ago                                             hungry_tesla
1daadaa55ea7   ubuntu                     "bash"                   10 days ago     Exited (0) 10 days ago                                               clever_swanson
9caeeff51191   ubuntu                     "bash"                   10 days ago     Exited (0) 10 days ago                                               happy_raman
f56326d3c24e   ubuntu                     "bash"                   10 days ago     Up 7 hours                                                           peaceful_roentgen
39199649ad60   hello-world                "/hello"                 10 days ago     Exited (0) 10 days ago                                               sad_chebyshev
feab4d15a9f7   centos                     "/bin/bash"              10 days ago     Exited (1) 10 days ago                                               pensive_gagarin
a50e05845b29   busybox                    "sh"                     10 days ago     Exited (0) 10 days ago                                               dreamy_robinson
9a76706d4b41   busybox                    "sh"                     10 days ago     Exited (137) 10 days ago                                             competent_haibt
ff1190e6c84f   busybox                    "sh"                     10 days ago     Exited (0) 10 days ago                                               hopeful_sinoussi
f99d3e87bba9   python                     "python3"                10 days ago     Exited (0) 10 days ago                                               pedantic_ishizaka
a843a39f5097   python                     "python3"                10 days ago     Exited (0) 10 days ago                                               inspiring_moore

```  

## Searching By Container Name  

Example of a basic filter via `docker container ls -a -f name=my` (using [my container listing](operating_systems/docker/container_commands?id=filtering-listed-containers) as a base):  
```
~# 
CONTAINER ID   IMAGE     COMMAND   CREATED      STATUS                  PORTS     NAMES
9f51eeae163c   ubuntu    "bash"    9 days ago   Exited (0) 9 days ago             my-brent
596f3956b190   ubuntu    "bash"    9 days ago   Exited (0) 9 days ago             myBrent
a50e05845b29   busybox   "sh"      9 days ago   Exited (0) 9 days ago             dreamy_robinson
```  
* Note that `name=my` was not a strict search - it included all names that had the word 'my' in it.  
* `name=my` also seems to be case-sensitive. 
* The flags `-f` and `--filter` are interchangeable.

## Searching By Exposed Ports  

You can use a filter to search by [exposed ports](operating_systems/docker/docker_basics?id=port-basics) across all of your containers via `--filter expose=xyz`; for example, if we wanted to see all containers with an exposed port `80` we could use `docker container ls -af expose=80` (using [my container listing](operating_systems/docker/container_commands?id=filtering-listed-containers) as a base):  
```
CONTAINER ID   IMAGE     COMMAND                  CREATED        STATUS        PORTS                                     NAMES
0c2fa71af02d   nginx     "/docker-entrypoint.…"   14 hours ago   Up 14 hours   0.0.0.0:49153->80/tcp, :::49153->80/tcp   wizardly_lovelace
5f7f4fcad376   nginx     "/docker-entrypoint.…"   20 hours ago   Up 20 hours   0.0.0.0:8080->80/tcp, :::8080->80/tcp     jovial_khayyam
9238ab2c314f   nginx     "/docker-entrypoint.…"   20 hours ago   Up 20 hours   80/tcp                                    elastic_lichterman
```  
* The flags `-f` and `--filter` are interchangeable.  

You can also use a range; for example, if you wanted to see all exposed ports in the range of 80 to 90 you could use:  
```
docker container ls -af expose=80-90
```  

Finally, if you wanted to also expressly indicate tcp ports specifically you could do that like so:  
```
docker container ls -af expose=80-90/tcp
```  


## Searching By Published Ports  

You can use a filter to search by [published ports](operating_systems/docker/docker_basics?id=port-basics) across all of your containers via `--filter publish=xyz`; for example, if we wanted to see all of the host's ports that mapped one if its own published ports `8080` to a container, we could use `docker container ls -af publish=8080` (using [my container listing](operating_systems/docker/container_commands?id=filtering-listed-containers) as a base):  
```
CONTAINER ID   IMAGE     COMMAND                  CREATED        STATUS        PORTS                                   NAMES
5f7f4fcad376   nginx     "/docker-entrypoint.…"   20 hours ago   Up 20 hours   0.0.0.0:8080->80/tcp, :::8080->80/tcp   jovial_khayyam
```  
* The flags `-f` and `--filter` are interchangeable.  

You can also use a range; for example, if you wanted to see all published ports in the range of 8000 to 50000 you could use:  
```
docker container ls -af publish=8080-50000
```  

Which would yield these results using [my container listing](operating_systems/docker/container_commands?id=filtering-listed-containers) as a base:  
```
CONTAINER ID   IMAGE     COMMAND                  CREATED        STATUS        PORTS                                     NAMES
0c2fa71af02d   nginx     "/docker-entrypoint.…"   14 hours ago   Up 14 hours   0.0.0.0:49153->80/tcp, :::49153->80/tcp   wizardly_lovelace
5f7f4fcad376   nginx     "/docker-entrypoint.…"   20 hours ago   Up 20 hours   0.0.0.0:8080->80/tcp, :::8080->80/tcp     jovial_khayyam
```  
* This found two published ports - 8080 and 49153 - mapped to a container.  

Finally, if you wanted to also expressly indicate tcp ports specifically you could do that like so:  
```
docker container ls -af publish=8080-50000/tcp
```  

## Formatting a Search

Its possible to format the results of a search with the `--format` option. Formatting is nice, as you get to pick the fields; in addition, there are several fields that are _only_ available with a format and not available in the vanilla search. Formats use a Go template to achieve its goal.  

The available fields are:  

| Field | Description |  
| --- | --- |  
| .ID | Container ID. |  
| .Image | Image ID. |  
| .Command | Quoted command. |  
| .CreatedAt | Time when the container was created. |  
| .RunningFor | Elapsed time since the container was started. |  
| .Ports | [Exposed ports](operating_systems/docker/docker_basics?id=port-basics). |  
| .Status | Container status. |  
| .Size | Container disk size. |  
| .Names | Container names. |  
| .Labels | All [labels](operating_systems/docker/container_commands?id=labels) assigned to the container. |  
| .Label | Value of a specific label for this container). See below for an individual example. |  
| .Mounts | Names of the [volumes](operating_systems/docker/docker_basics?id=creating-a-mounted-volume) mounted in this container. |  
| .Networks | Names of the networks attached to this container. |  



An example:  
```
docker container ls -a --format 'table {{.Names}}\t{{.Image}}\t{{.Mounts}}\t{{.Size}}\t{{.Label "type"}}'
```  
* Note the _entire_ thing is wrapped in single quotes. You could also use double quotes, but I used them to identify the unique label.  
* Each selection is wrapped in `{{}}`.  
* The word 'table' prints the headers of the columns; you can leave this off if you wish, but I prefer it.  
* You can use a `\t` (tab) for nice spacing.  
* The `.Label` uses the [key](operating_systems/docker/container_commands?id=labels) in the key/value pair; the values are printed (above, the arbitrary key `type` is used). Note the use of _both_ single and double quotes.  

# Deleting Containers  

!> A [container](operating_systems/docker/docker_basics?id=container) _must_ not be running when you delete it! If it is running, you can use [stop](operating_systems/docker/container_commands?id=stop).  

To delete / remove a container, run:  
```
docker rm [CONTAINER_NAME]
```  
* Do _not_ include the brackets `[]`.  
* This can also be the [long or short UUID](operating_systems/docker/container_commands?id=naming-a-container).  
* You can also include the `-f` tag to force delete the container.  

---  

# Exec 

The `docker exec` command executes the given command in the listed container (which means _the container must be running for `exec` to work_), returning the output (if any) to your screen. The basic structure is:  
```
docker container exec [CONTAINER_NAME] [COMMAND]
```  
* Do _not_ include the brackets `[]`.  
* The word `container` is optional.  
* You can replace `[CONTAINER_NAME]` with the [short UUID](operating_systems/docker/container_commands?id=naming-a-container).  

The `[COMMAND]` is the _full_ command you wish to run. If, for example, you wanted to get the network config from inside the container itself, you could run `docker container exec [CONTAINER_NAME] ifconfig -a` , which runs `ifconfig -a` inside the container and prints the output to your screen.  

> If you simply want to print the output of a command to your screen and exit immediately, do _not_ use the `-it` flags!!!  

## Enter Running Container  

One of the most popular things to do with `exec` is to enter a running [container](operating_systems/docker/docker_basics?id=container):  
```  
docker container exec -it [CONTAINER_NAME] sh
```  
* Do _not_ include the brackets `[]`.  
* You can replace `[CONTAINER_NAME]` with the [short UUID](operating_systems/docker/container_commands?id=naming-a-container).  
* The `sh` describes the shell - sometimes you can switch this out with `bash` or others (but not always).  

> Exiting the container using `exit` will _not_ shut down the container under most circumstances!  

## Running Commands as User  

To run a command as a _specific_ user you can use the `--user` flag:  
```
docker container exec --user [SOME_USER] [CONTAINER_NAME] whoami
```  
* Do _not_ include the brackets `[]`.  
* You can replace `[CONTAINER_NAME]` with the [short UUID](operating_systems/docker/container_commands?id=naming-a-container).  
* This simply prints the current username (which should be the one you switched to).  

## Running Commands In Different Dir  

If you wish to run a command from a specific directory, you can use the `--workdir` directory:  
```
docker container exec --workdir /app [CONTAINER_NAME] pwd
```  
* Do _not_ include the brackets `[]`.  
* You can replace `[CONTAINER_NAME]` with the [short UUID](operating_systems/docker/container_commands?id=naming-a-container).  
* This simply prints the directory we switched to (`/app`), but this has other obvious uses.  

---  

# Copying files to/from Container  

> Any changes you make in this container will be local to this container - _not_ its associated image.  

You can copy files to a container like so:  
```
docker container cp [SOMEFILE.txt] [CONTAINER_NAME]:/path/to/file/in/container.txt
```  
* Do _not_ include the brackets `[]`.  
* `CONTAINER_NAME` can also be the [long or short UUID](operating_systems/docker/container_commands?id=naming-a-container).  
* This may be different for Windows hosts, but should be the same for Mac users.  

You can also copy a file _from_ the container to the host like so:  
```
docker container cp [CONTAINER_NAME]:/path/to/file/in/container.txt [SOMEFILE.txt]
```  

---

# Mounting Drives  

Docker can [mount](operating_systems/ubuntu/linux_notes?id=mounting-unmounting-drives) directories from your host machine so they appear as a directory in your [container](operating_systems/docker/docker_basics?id=container).

## Bind Mounts  

The quickest way to [mount](operating_systems/ubuntu/linux_notes?id=mounting-unmounting-drives) a directory on the host machine to your [container](operating_systems/docker/docker_basics?id=container) is via a <font color="green">bind mount</font> when [creating](operating_systems/docker/container_commands?id=run-in-background) the container. To do this:
```
docker container run -dit --name mount_test --mount type=bind,source=/var/lib/docker/volumes/first_volume/_data,target=/opt/dir_in_container ubuntu
```  
* See [here](operating_systems/docker/container_commands?id=run-in-background) for info on the `run` command, the `-dit` flags, etc.  
* We name this container `mount_test`.  
* The `--mount` flag initiates the mount.  
   * The type is `bind`.  
   * The `source` is the directory on the _host_ machine that we are going to mount in the container.  
   * The `target` is the directory in the container itself that will mount back to the `source` directory on the host.  

> You can make the above *read-only* by putting a `:ro` _directly_ at the end of the `target`.  

## Mounting With a Volume

You can also mount a host directory using the [volume](operating_systems/docker/volume_commands) system; this is achieved by using the `--volume` parameter:  
```
docker container run -dit --name mount_test -v /var/lib/docker/volumes/first_volume/_data:/opt/dir_in_container:rw ubuntu
```  
* the format here is `-v DIRECTORY_ON_HOST:DIRECTORY_IN_CONTAINER`  
* The `:rw` means 'read / write' and is the default (you dont actually have to use this if you with for read / write permission on the mount).  
   * Again, you can make the above *read-only* by putting a `:ro` _directly_ at the end of the `DIRECTORY_IN_CONTAINER`, if you wish to do so.  
   
> To see when to use a bind mount vs when you should use a volume, I try to talk about this [here](operating_systems/docker/volume_commands?id=bind-mounts-vs-volumes-which-is-best).  

---  

# Display Container Processes  
<!-- BRENT - flesh this out more -->

To display a container's processes, run:  
```
docker container top [CONTAINER_NAME]
```  
* Do _not_ include the brackets `[]`.  
* This can also be the [long or short UUID](operating_systems/docker/container_commands?id=naming-a-container).  

---  

# Display Container Logs    
<!-- BRENT - flesh this out more -->

To display a container's logs, run:  
```
docker container logs [CONTAINER_NAME]
```  
* Do _not_ include the brackets `[]`.  
* This can also be the [long or short UUID](operating_systems/docker/container_commands?id=naming-a-container).  

---  

# Monitoring Container Statistics  
<!-- BRENT - flesh this out more -->

You can monitor your container's statistics with the following command:  
```
docker container stats
```  

---  

<!--
Finish:
* man docker-container-ls
* man docker-run
-->