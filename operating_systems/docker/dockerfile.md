# Dockerfile  

# Description  

At some point, very early on in your Docker experience, you _will_ need to write a script to build a _custom_ Docker image; its inevitable. You _will_ need an image that has unique environment variables, specific apps, specific directories, specific uers, etc; and if you are using Docker to deploy personal / corporate apps, these apps will be updated frequently - so you will need to develop a new Docker image to support this frequently.  

You can build your own 'image' via a Docker script; this script is more commonly known as a <font color="green">Dockerfile</font>. A <font color="green">Dockerfile</font> uses easy-to-understand syntax to build a new 'image'.  I will delve into the <font color="green">Dockerfile</font> in this section.  

# Basic Dockerfile Example  

It may be best to start out with a basic Dockerfile example:  
```
FROM alpine:3.15.4

ENV SOMEVAR1="My Variable"
ARG SOME_OTHER_DIR=someOtherInternalDir

RUN apk add bash openjdk11-jre

RUN mkdir -p /app/${SOME_OTHER_DIR}

COPY myTestTextFile.txt /app/${SOME_OTHER_DIR}/myTestTextFile.txt

```  
* This file is quite literally named `Dockerfile`.  
   * It's a good idea to keep the `Dockerfile` - and most (if not all) of the local host files it references - in the same directory.  
   * You _can_ rename this if you wish, but `Dockerfile` is the default expected filename.  
* The first word in a line - for example, `FROM`, `ENV`, `ARG`, etc - are known as <font color="green">instructions</font>.  
   * The <font color="green">instruction</font> does _not_ have to be all caps, but it is the convention to do so.  
* Each <font color="green">instruction</font> is executed when the last one finishes.  

## Comments in Dockerfiles  

A comment in a Dockerfile starts with a `#`; the rest of that line is considered a comment and will not affect the build.  

# Creating / Running Dockerfiles  

Compiling a Dockerfile into an [image](operating_systems/docker/docker_basics?id=docker-image-term) is described [here](operating_systems/docker/image_commands?id=creating-images-dockerfile), and turning the image into a [container](operating_systems/docker/docker_basics?id=container) and running it is described [here](operating_systems/docker/container_commands?id=running-containers).  

# Image Layers  

Docker tries to be as efficient as possible; one of the ways it achieves this is when it builds an image, it tries to use as much of other images - previously built - as it can. Each individual 'instruction' (i.e. exactly one command line) in a Dockerfile constitutes an <font color="green">Image Layer</font>. This goes far further than just saving time during the build: re-used <font color="green">image layers</font> are _also_ re-used on the hard drive, saving physical disk space. 

As an example here are the first few output lines from a Dockerfile I compiled:  
```
Step 1/11 : FROM alpine:3.15.4
3.15.4: Pulling from library/alpine
df9b9388f04a: Pull complete 
Digest: sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454
Status: Downloaded newer image for alpine:3.15.4
 ---> 0ac33e5f5afa
Step 2/11 : ENV SOMEVAR1="My Variable"
 ---> Running in d5eae19c8c80
Removing intermediate container d5eae19c8c80
 ---> fb42cd2fef61
Step 3/11 : ENV SOMENUM=57
 ---> Running in 438b7394d78d
Removing intermediate container 438b7394d78d
 ---> afefde196573
Step 4/11 : ARG SOME_OTHER_DIR=someOtherInternalDir
 ---> Running in 4c38949a2abb
Removing intermediate container 4c38949a2abb
 ---> e3cfb2a174a1
Step 5/11 : CMD ["./binbash.sh"]
 ---> Running in f59621db77cd
Removing intermediate container f59621db77cd
 ---> c2a2fd79b00a
Step 6/11 : LABEL maintainer="Brent Wagenseller brent.wagenseller@vandelayindustries.com"
 ---> Running in fa3729b8c15d
Removing intermediate container fa3729b8c15d
 ---> fd8a21aa7efe
Step 7/11 : LABEL robot="Chicken special"
 ---> Running in 3c90fc389aee
Removing intermediate container 3c90fc389aee
 ---> e1eacd7247ce

```  
* Note - I truncated the results (it stops at step 7 of 11 above).  


Then, I changed line 5 from `CMD ["./binbash.sh"]` to `CMD ["/bin/bash"]`:
```
Step 1/11 : FROM alpine:3.15.4
 ---> 0ac33e5f5afa
Step 2/11 : ENV SOMEVAR1="My Variable"
 ---> Using cache
 ---> fb42cd2fef61
Step 3/11 : ENV SOMENUM=57
 ---> Using cache
 ---> afefde196573
Step 4/11 : ARG SOME_OTHER_DIR=someOtherInternalDir
 ---> Using cache
 ---> e3cfb2a174a1
Step 5/11 : CMD ["/bin/bash"]
 ---> Running in 4d18cbfdc36d
Removing intermediate container 4d18cbfdc36d
 ---> 02257961e0b0
Step 6/11 : LABEL maintainer="Brent Wagenseller brent.wagenseller@vandelayindustries.com"
 ---> Running in 285ae998e7cc
Removing intermediate container 285ae998e7cc
 ---> fb2b7c909091
Step 7/11 : LABEL robot="Chicken special"
 ---> Running in 72ec19dcb8c7
Removing intermediate container 72ec19dcb8c7
 ---> 5045f5752662
```  
* Note - I truncated the results (it stops at step 7 of 11 above).  

If you compare the first and second runs, you will see steps 2-4 say `Using cache` (with the first one simply referencing the image ID of alpine, `0ac33e5f5afa`); this `Using cache` indicates the second image will re-use <font color="green">image layers</font> 1-4 from the _first_ run. For instructions that make an <font color="green">image layer</font> out of, say, a defined ENV variable may not save us too much space by re-using that <font color="green">image layer</font>, an instruction that, say, installs Java could make a _considerable_ difference in size.  

Re-using <font color="green">image layers</font> can be a bit touchy - if the order changes from one Dockerfile to another _at all_ - or if a single instruction is changed, at all - the change (and _all_ potential <font color="green">image layers</font> after that image change) _cannot_ be re-used. A big implication of this is you should be very careful about the layout of your Dockerfile: write it as such that instructions that will not change should be at the top, instructions that _may_ change should be towards the middle, and instructions that will _probably_ change should be put at the end of the Dockerfile.  

With this in mind:  
* Assigning a new value to an ARG or ENV parameter will force that instruction - and all subsequent instructions - to change and will have to be re-built.  
   * That said, if you [overwrite an ARG when performing the image build](operating_systems/docker/image_commands?id=overwriting-arguments) this does _not_ seem to force subsequent <font color="green">image layers</font> to be re-built; they can be re-used.  
* If possible, lump as many instructions as you can into a single instruction.  
   * When doing this, try to lump them with similar instructions that will have a similar probability of being altered.  
* If the [command](operating_systems/docker/dockerfile?id=command-run-on-start) you include is to run a shell script - and you use the same name of that script across all Docker images and containers - its best to put this at the top of the Dockerfile.  
   * Even if the _body_ of the shell script you import later in the Dockerfile via a [COPY](operating_systems/docker/dockerfile?id=copy-copy-files-from-host-to-image) command changes, the <font color="green">image layer</font> around the CMD instruction will _not_ change (_unless_ you change the shell script name).  


## Image Layers in Multi-Stage Dockerfiles 

[multi-stage Dockerfiles](operating_systems/docker/dockerfile?id=multistage-dockerfiles) will make multiple layers. If an instruction changes for one stage, the rest of the instructions for that particular stage will _not_ use a cache. That said, subsequent stages will attempt to use their own caches. In short, if one instruction changes for one stage, it will _not_ affect other stages.  

# FROM - Setting the Image Base  

> The <font color="green">FROM</font> instruction is usually the first line in a Dockerfile.  

In almost all circumstances, you will use another already created image to build your new image. The <font color="green">FROM</font> instruction gives you a chance to set the image; it uses the format `REPOSITORY:TAG` (as described [here](operating_systems/docker/image_commands?id=identifying-images)), but if you leave off the `:` and the tag it will simply grab the `latest` tag. A list of possible initial images are [here](operating_systems/docker/docker_basics?id=locations-of-pre-built-images).  

An example of the <font color="green">FROM</font> instruction:
```
FROM alpine:3.15.4
```  
* This uses the `alpine` repository - specifically the tag `3.15.4` - as the base for your new image, but you can pick any [image](operating_systems/docker/docker_basics?id=locations-of-pre-built-images) you like.  

> Note the image in the <font color="green">FROM</font> instruction is not altered itsself via Dockerfile instructions, but the finalized image - which is different from the one in the <font color="green">FROM</font> instruction - will be potentially altered.  

## Multistage Dockerfiles  

A <font color="purple">FROM</font> instruction in a Dockerfile technically signifies a new <font color="green">stage</font> in the Dockerfile <font color="green">Multistage Dockerfile</font>. Each instruction following a <font color="purple">FROM</font> instruction will be applied to the image listed in the <font color="purple">FROM</font>, but if another <font color="purple">FROM</font> instruction is introduced, all subsequent instructions will be excecuted on that image instead - _unless_ the instruction uses the `--from` parameter (and specifies a specific stage). The [COPY](operating_systems/docker/dockerfile?id=copy-copy-files-from-host-to-image) instruction can do this, meaning you can compile code in one stage and then COPY a _specific_ file from that previous <font color="green">stage</font> to your new <font color="green">stage</font>, making it possible for Docker to act as a [build server](operating_systems/docker/dockerfile?id=docker-as-build-server).  

To create a name for a <font color="green">stage</font> / begin a new <font color="green">stage</font>, simply append an `AS [STAGE_NAME]` onto the end of a <font color="purple">FROM</font> instruction like so:  
```
FROM alpine:3.15.4 AS [STAGE_NAME]
```  
* Do _not_ include the brackets `[]`.  
* `STAGE_NAME` is defined by you.
* This uses the `alpine` repository - specifically the tag `3.15.4` - as the base for your new image, but you can pick any [image](operating_systems/docker/docker_basics?id=locations-of-pre-built-images) you like.  

Only the information in the _final_ stage makes it to the final image created; if you want any information gathered in an earlier <font color="green">stage</font>, you must expressly do that in the Dockerfile; otherwise, its gone forever.  

> Put _as little as possible_ in the final stage. Do not put build tools, curl, etc etc in the final stage as that will just give the final stage a larger footprint, in addition to exposing more vulnerabilities for hackers (as the more tools / apps you have, the more likely there is a vulnerability).  


# MAINTAINER - Identifying the Creator  

The <font color="green">MAINTAINER</font> instruction lists the creator of the Dockerfile in the form of an email address. Its usage is like so:  
```
MAINTAINER brent.wagenseller@vandelayindustries.com  
```  
* There should be one and only one <font color="green">MAINTAINER</font> instruction.  

!> The <font color="green">MAINTAINER</font> instruction is actually deprecated, and you should be using a [label](operating_systems/docker/dockerfile?id=label-setting-image-labels) instead to capture the maintainer a la `LABEL maintainer="Brent Wagenseller brent.wagenseller@vandelayindustries.com"`

# LABEL - Setting Image Labels  

The <font color="green">LABEL</font> instruction adds [image labels](operating_systems/docker/image_commands?id=labels-in-images) to your image; an example of a label is:  
```
LABEL maintainer="Brent Wagenseller brent.wagenseller@vandelayindustries.com"
```  

Later on, you can [inspect the image](operating_systems/docker/image_commands?id=inspecting-images) and it will list the labels; for example, the above plus one other label is in an image I just built:  
```
...
            "Labels": {
                "maintainer": "Brent Wagenseller brent.wagenseller@vandelayindustries.com",
                "program": "Robot Chicken"
            }
...
```  

You are encouraged to add multiple labels to your image in order to identify it; [here](operating_systems/docker/image_commands?id=labels-in-images) are some suggestions.  

# ENV - Setting Environment Variables  

You can use the <font color="green">ENV</font> instruction to set environment variables (variables that can be recalled from within the OS (or in our case, Container) itself).  Its common for apps - which you can place in the container with the `COPY` instruction - to recall environment variables too, so its important to know how to do this.  

An example of the <font color="green">ENV</font> instruction:
```
ENV SOMEVAR1="My Variable"
```  
* This sets the environment variable `SOMEVAR1` to `My Variable`.  
* The double quotes are not strictley needed (but since a whitespace is involved, I like to use it).  
* You can have multiple <font color="green">ENV</font> instructions which sets multiple environment variables.  

You can also chain <font color="green">ENV</font> instructions like so:  
```
ENV SOMEVAR1="My Variable" \ 
    SOME_OTHER_ENV=12345
```  

The difference between variables set by <font color="green">ENV</font> and those set by [ARG](operating_systems/docker/dockerfile?id=arg-setting-internal-arguments) is <font color="green">ENV</font> variables can be referenced _in_ the container while its running, whereas <font color="green">ARG</font> is _only_ available during the image build.  

# ARG - Setting Internal Arguments  

The <font color="green">ARG</font> instruction simply sets an argment / parameter that is _internal_ to the Dockerfile itself; this argument can be referenced - and used - anywhere in the Dockerfile, but once your new image is completed, this parameter will never be referenced again.  

An example of the <font color="green">ARG</font> instruction:
```
ARG SOME_OTHER_DIR=someOtherInternalDir
```  
* This sets the parameter `SOME_OTHER_DIR` to `someOtherInternalDir`, which can be referenced later on in the Dockerfile script with the `${}` (for example, you could use `${SOME_OTHER_DIR}` as it is done in [the example](operating_systems/docker/dockerfile?id=basic-dockerfile-example) Dockerfile, and it would replace that with `someOtherInternalDir` at runtime).  
* While <font color="green">ARG</font> is helpful to save time if you have a parameter that can change in multiple spots, it is _also_ used if you want to override this parameter at compile time - see [here](operating_systems/docker/image_commands?id=overwriting-arguments) on how to do that).  
* The double quotes are not strictley needed around the variable (I did not do it above, but I typically like to use them).  
* You can have multiple <font color="green">ARG</font> instructions which sets multiple parameters.  

You can also chain <font color="green">ARG</font> instructions like so:  
```
ARG SOME_OTHER_DIR=someOtherInternalDir \ 
    SOME_OTHER_ARG=12345
```  

# WORKDIR - Change Directory  

The <font color="green">WORKDIR</font> instruction does two things:  
1\. Create a directory (if it does not exist).  
2\. Change the working directory of the _image being built_ (which can affect the [RUN](operating_systems/docker/dockerfile?id=run-running-commands-on-the-image) instruction).  

An example:   
```
WORKDIR /app/someApp
```  
* This creates the directory `/app/someApp` (if it does not exist) and also changes the directory of the _image being built_ to `/app/someApp`.  


# RUN - Running Commands on the Image  

The <font color="green">RUN</font> instruction allows you to run commands in the image you loaded from [the FROM instruction](operating_systems/docker/dockerfile?id=from-setting-the-image-base). This instruction is usually a requirement, as you will almost _certainly_ have to make a directory, create a user, install packages, etc in your new image.  

It appears that the commands run with the <font color="green">RUN</font> instruction are run as root. The exact commands vary from image to image, so there is no standard for <font color="green">RUN</font> after the word <font color="green">RUN</font> itself - several of the most common commands for Ubuntu are [here](operating_systems/ubuntu/linux_notes), but again, these can be _completely_ different and may not work for a different image. An example of installing two packages in an [Alpine](https://hub.docker.com/_/alpine) image - Java 11 and the bash shell - is accomplished like so:  
```
RUN apk add bash openjdk11-jre
```  
* `openjdk11-jre` installs Java 11, and `bash` installs the bash shell.  

You can also chain commands if you append a `&& \` at the end of the <font color="green">RUN</font> instruction; for example, if this is run on an [Alpine](https://hub.docker.com/_/alpine) image, it will add an `/app` directory and _then_ install the `bash` script:  
```
RUN mkdir -p /app && \
    apk add bash 
```  
* I prefer this method, as it condenses multiple [image layers](operating_systems/docker/dockerfile?id=image-layers) into a single image layer.  

> Note that you can use the [exec form](operating_systems/docker/dockerfile?id=shell-vs-exec-form) with the <font color="green">RUN</font> instruction, but I normally use the shell form (the examples above are in the shell form).  

# COPY - Copy Files From Host To Image  

It's possible to copy files from your host system to the image you are creating with the <font color="green">COPY</font> instruction; the basic setup is `COPY /DIR_ON_LOCAL_HOST/FILE_ON_LOCAL_HOST /DIR_IN_NEW_IMAGE/FILE_NAME_IN_NEW_IMAGE`, but an example wout be:  
```
COPY myTestTextFile.txt /app/myTestTextFile.txt
```  
* This assumes you have a file named `myTestTextFile.txt` in your working directory (that said, you could list an absolute path here).  
* You have previously made the `/app` directory in your new image.  
* If you use a dot (`.`) in place of `/app/myTestTextFile.txt`, it will just copy the file to the [working directory](operating_systems/docker/dockerfile?id=workdir-change-directory) and use the same filename as it exists in your local machine.  

## Copying Multiple Files 

You could use two dots (`. .`) which would mean 'copy all files from the local directory to the new image's [working directory](operating_systems/docker/dockerfile?id=workdir-change-directory) like so:  
```
COPY . .
```  

However, if you wanted to explicitly copy all of the contents of a directory on your local machine to the image being created, you would just name the target directories and put a slash at the end like so:  
```
COPY /usr/local/ /app/local/  
```  
This copies all files from the local `/usr/local` directory to the directory `/app/local` in the created image.  

Now, if you wanted to copy a _specific_ folder somewhere else you would do that like so:  
```
COPY /usr/local /app
```  
This copies the local directory `/usr/local` to the directory `/app` in the created image.  

## Copying From Another Stage  

Its possible to copy an image from a previous [stage](operating_systems/docker/dockerfile?id=multistage-dockerfiles) to the current stage with the <font color="green">COPY</font> instruction via the `--from` parameter:  
```
COPY --from=build /app/features.txt /app/features.txt
```  
* This [copies](operating_systems/docker/dockerfile?id=copy-copy-files-from-host-to-image) the file `/app/features.txt` from a previous stage named `build` to the current stage's `/app` directory.  

# EXPOSE - Exposing Container Ports  

If you know an application you will be running in the image (once its used in a [container](operating_systems/docker/docker_basics?id=container)) will need an [exposed port](operating_systems/docker/docker_basics?id=port-basics), you can expose said port with the <font color="green">EXPOSE</font> instruction like so:  
```
EXPOSE 8080/tcp  
```  
* This exposes the TCP port 8080, so 8080 is opened internally in the container (once built).  
   * If you wish to use the port as a TCP port, you do _not_ need to specify `/tcp` - TCP is the default.  
   * If you wish to use the port as a UDP port, you will be _required_ to append `/udp` at the end.  
* You can also do a range as well; so for example `EXPOSE 10000-15000/tcp` will expose TCP ports 10000 to 15000.  

You can also chain <font color="green">EXPOSE</font> instructions like so:  
```
EXPOSE 8080/tcp \ 
    10000-15000/tcp
```  

# COMMAND - Run On Start  

Once your image is created, it is ready to be launched as a container. Often, its helpful to define a command that is run when the [container](operating_systems/docker/docker_basics?id=container) _initially_ starts; this is where the <font color="green">CMD</font> instruction comes into play. For example, lets say you copied in shell script `/app/script.sh` into the image, it takes one string parameter (via a flag), and you want this script to run when the container _initially_ starts. You could specify this in a <font color="green">CMD</font> instruction like so:  
```
CMD /app/script.sh -msg "hello there"
```  
* This will run the line `/app/script.sh -msg "hello there"` in the container when it starts.  
   * The <font color="green">CMD</font> is _often_ some form of a shell script, as you can only have _one_ <font color="green">CMD</font> instruction.  
* Note this is in [shell form](operating_systems/docker/dockerfile?id=shell-vs-exec-form); you can also use [exec form](operating_systems/docker/dockerfile?id=shell-vs-exec-form) as well.  

There can only be one <font color="green">CMD</font> instruction - if you have multiple of them in your Dockerfile, only the last one will be considered. In addition, the <font color="green">CMD</font> instruction is _not_ run during image creation - so you can place it at the top of the Dockerfile with no consequence. As a matter of fact, if the [command](operating_systems/docker/dockerfile?id=command-run-on-start) you include is to run a shell script - and you use the same name of that script across all Docker images and containers - its best to put this at the top of the Dockerfile (although you may want to check out the warning on this [here](operating_systems/docker/dockerfile?id=shell-vs-exec-form)).  

Finally, A [container](operating_systems/docker/docker_basics?id=container) is typically meant to run a simple task and stop; you _can_ have it run indefinitely (like an operating system), but many containers are simply designed to complete a task and then stop once that is done. Therefore, most <font color="green">CMD</font> instructions will be a simple line that will launch an app, script, etc that has a _specific_ task and then the container will close once that is done. That said, If the app runs indefinitely (such as a command that starts a web service) - or if you use a shell as the <font color="green">CMD</font> instruction (i.e. `/bin/sh`, `/bin/bash`, etc) the container will also run indefinitely.   

> The difference between <font color="green">RUN</font> and <font color="green">CMD</font> is <font color="green">RUN</font> is used for the _initial_ build if your image, but <font color="green">CMD</font> will be called _every_ time an associated container starts up.  
  <br>

> In addition, there is a difference between the <font color="green">CMD</font> and [ENTRYPOINT](operating_systems/docker/dockerfile?id=entrypoint-run-on-start) instructions - as well as a unique relationship between `ENTRYPOINT` and <font color="green">CMD</font> - which is described in [that section](operating_systems/docker/dockerfile?id=entrypoint-run-on-start).

## Shell Vs. Exec Form  

> There is an in-depth explanation of this over at [gmaslowski.com](https://gmaslowski.com/docker-shell-vs-exec/).  

There are two ways you can run the [RUN](operating_systems/docker/dockerfile?id=run-running-commands-on-the-image), [CMD](operating_systems/docker/dockerfile?id=command-run-on-start), and [ENTRYPOINT](operating_systems/docker/dockerfile?id=entrypoint-run-on-start) instructions - in <font color="green">shell form</font> _or_ <font color="green">exec form</font>. Say, for example, we wanted to run `mkdir -p /app/images` with the <font color="green">shell form</font>; you could simply run:  
```
RUN mkdir -p /app/images  
```  

This is the easy way of running commands, but it comes at a price - the commands are run thorugh the shell (i.e. `/bin/sh -c` is run before all commands) and are _not_ run directly. This has some advantages (namely, you can pass in [environment variables](operating_systems/docker/dockerfile?id=env-setting-environment-variables) or [argument variables](operating_systems/docker/dockerfile?id=arg-setting-internal-arguments) using the <font color="green">shell form</font>, whereas that is not possible with the <font color="green">exec form</font>), but there are disadvantages as well. The main disadvantage, as pointed out over at [gmaslowski.com](https://gmaslowski.com/docker-shell-vs-exec/), is that when you try to run a [stop](operating_systems/docker/container_commands?id=stop) on a container that ran a <font color="green">shell form</font> [CMD](operating_systems/docker/dockerfile?id=command-run-on-start) or [ENTRYPOINT](operating_systems/docker/dockerfile?id=entrypoint-run-on-start) instruction, the graceful `SIGTEM` sent from Docker to the process is intercepted by the shell, which does _not_ pass on the `SIGTERM` to the processes it started - so there is no graceful shutdown of any app you may have started. Docker also uses a `stop_grace_period`, but since your app can never resoond during that timeframe, a `SIGKILL` will be sent which is a forceful shutdown od the container.  

On the other hand, you could use the <font color="green">exec form</font> like so:  
```
RUN ["mkdir", "-p", "/app/images"]  
```  

This is a list, and every portion of the command that is separated by whitespace is in a different sequential position in the list.  

The advantage of using the <font color="green">exec form</font> is its run _directly_ and not through a shell; in addition, any app run with this method will accept the `SIGTERM` sent by the [stop](operating_systems/docker/container_commands?id=stop) command and will thus gracefully shut down. The downsides of using <font color="green">exec form</font> are:  
* This cannot accept [environment variables](operating_systems/docker/dockerfile?id=env-setting-environment-variables) or [argument variables](operating_systems/docker/dockerfile?id=arg-setting-internal-arguments) - the entries in the list must seemingly be hardcoded.  
* You cannot run multiple commands with this (as you could in the [RUN](operating_systems/docker/dockerfile?id=run-running-commands-on-the-image) example); the <font color="green">exec form</font> does _not_ allow for multiple commands, its one command only!  

In my opinion, you should use the <font color="green">shell form</font> when:  
* issuing [RUN](operating_systems/docker/dockerfile?id=run-running-commands-on-the-image) instructions (_unless_ the instruction has trouble with being run with `/bin/sh -c`), as these commands only execute during the build of the image and are not affected by the [stop](operating_systems/docker/container_commands?id=stop) command.  
   * Also the ability to issue _nultiple_ `RUN` instructions is very useful here.  
* A [CMD](operating_systems/docker/dockerfile?id=command-run-on-start) or [ENTRYPOINT](operating_systems/docker/dockerfile?id=entrypoint-run-on-start) instruction requires the explicit use of an [environment variable](operating_systems/docker/dockerfile?id=env-setting-environment-variables) in the command issued.  

and you should use the <font color="green">exec form</font> when:  
* You want to run _any other_ shell that is not `sh` (i.e. `/bin/bash`).  
* You are using the [CMD](operating_systems/docker/dockerfile?id=command-run-on-start) or [ENTRYPOINT](operating_systems/docker/dockerfile?id=entrypoint-run-on-start) instruction, only have one line to run, use no [environment variables](operating_systems/docker/dockerfile?id=env-setting-environment-variables) in the command issued, and you would like the command to shut down gracefully upon an issued [stop](operating_systems/docker/container_commands?id=stop).  

!> Many Dockerfiles simply use a shell script in the [CMD](operating_systems/docker/dockerfile?id=command-run-on-start) or [ENTRYPOINT](operating_systems/docker/dockerfile?id=entrypoint-run-on-start) instruction to launch other scripts; doing so may give you the _same_ issues as running in <font color="green">shell form</font> - as you are still relying on a shell - even if you used the <font color="green">exec form</font>. This may depend on the shell in the script, as all shell scripts do not behave the same - but you should research how your chosen shell scripts handle `SIGTERM` in order to make sure that is being done properly. Also, there may be some workarounds to this - see [stackoverflow.com](https://stackoverflow.com/questions/68523106/docker-bash-shell-script-does-not-catch-sigint-or-sigterm) and also [here](https://stackoverflow.com/questions/50257930/docker-run-script-to-catch-interruption-signal).  

# ENTRYPOINT - Run On Start  

The <font color="green">ENTRYPOINT</font> instruction is very similar to the [CMD](operating_systems/docker/dockerfile?id=command-run-on-start) instruction, but the main difference is the [CMD](operating_systems/docker/dockerfile?id=command-run-on-start) instruction _can_ be overwritten when [the container is initially run](operating_systems/docker/container_commands?id=running-containers) while the <font color="green">ENTRYPOINT</font> instruction _cannot_ be overwritten.  

In addition, _if_ you use the <font color="green">ENTRYPOINT</font> instruction, it changes the way the `CMD` instruction works: when the <font color="green">ENTRYPOINT</font> instruction is present, `CMD` becomes the _parameters_ for the <font color="green">ENTRYPOINT</font> instruction. For example, say we have these two lines in a Dockerfile (note the [exec form](operating_systems/docker/dockerfile?id=shell-vs-exec-form) is used in this example - both shell form and exec form can be used by the <font color="green">ENTRYPOINT</font> instruction):  
```
...
ENTRYPOINT ["echo", "hello"]
CMD ["world"]
...
```  

If we were to [compile this Dockerfile into an image](operating_systems/docker/image_commands?id=creating-images-dockerfile) (naming the image `hello_world`) and then we then [built and ran the image as a container](operating_systems/docker/container_commands?id=running-containers) with the command `docker container run -it hello_world` we would see the following output:  
```
hello world
```  

It is apparent that since we did not feed any additional parameters to our <font color="green">ENTRYPOINT</font> instruction in the run command, the word "world" was used as a default. If we added the word `Teddy` on the end of the run command (i.e. `docker container run -it hello_world Teddy`), the word 'Teddy' would be considered a parameter that needs to be fed to the `ENTRYPOINT` instruction. We would see the following:  
```
hello Teddy
```  

So, since we had an <font color="green">ENTRYPOINT</font> instruction in our Dockerfile, the `CMD` instruction became the _default parameters_ used at the end of the <font color="green">ENTRYPOINT</font> instruction (note we only had one additional parameter - the word `world` - in our `CMD` parameter listing, but this can be an arbitrary number of entries separated by commas). In effect, the `CMD` parameter entries are added to the end of the <font color="green">ENTRYPOINT</font> instruction, and these _can_ be overwritten when the container is created / ran. The great advantage of this is we can set arbitrary parameters here (via the [run](operating_systems/docker/container_commands?id=running-containers) command) _while_ using the [exec form](operating_systems/docker/dockerfile?id=shell-vs-exec-form) in the <font color="green">ENTRYPOINT</font> instruction; usually the exec form is hardcoded / you cannot use variables, but this is a way around that (at least for the <font color="green">ENTRYPOINT</font> instruction).  

---  

# Docker as Build Server  

Docker can act as a build server with the clever usage of Dockerfiles. You can make a [multi-stage Dockerfile](operating_systems/docker/dockerfile?id=multistage-dockerfiles) like so:  
```
FROM maven:latest AS build

RUN <Maven commands>

FROM alpine:3.15.4 AS production

COPY --from=build <Copy file from the 'build' stage to this 'production' stage>
```  

The above is incomplete - the things in between `<>` are instructions that you will have to fill in. That said, this gives you an idea on how to get started using Docker as a build 'server' - it can handle the build portion, the testing, and then the final production image.  

This works because if _any_ step fails (including the [mvn install](learn_to_code/java/maven?id=compiling-your-maven-project) command) the _entire_ Dockerfile image creation will fail.  