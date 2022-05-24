# Docker Image Commands  

# Must Be Run As Root

This must be stated - almost all commands _must_ be run as root! There is a way to get around this, but there seems to be constant problems with it (the one I ran into revolved around [/var/run/docker.sock](https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue) which _requires_ you set this file's [chmod](operating_systems/ubuntu/linux_notes?id=changing-permissions) to `666` for every re-set, as well as [adding the user](operating_systems/ubuntu/linux_notes?id=adding-users-to-groups) to the `docker` group). Because of this, I run all commands as root.   

---  

# Images  

> To see some pre-built [images](operating_systems/docker/docker_basics?id=docker-image-term), go [here](operating_systems/docker/docker_basics?id=locations-of-pre-built-images).  

[Images](operating_systems/docker/docker_basics?id=docker-image-term) act as a base to [containers](operating_systems/docker/docker_basics?id=container); they are downloaded to the host and need to be occasionally managed. This section will detail how to manage [images](operating_systems/docker/docker_basics?id=docker-image-term).  

## Identifying Images  

Images are identified by a 'repository' name as well as a 'tag'. Multiple images can have the same 'repository' name, so long as they each have a different 'tag'. In practice, the 'repository' identifier can act as an OS or application name and the 'tag' can act as the version.   

When referencing both, use the format `REPOSITORY:TAG`; as a matter of fact, `REPOSITORY:TAG` is known as the <font color="green">reference</font> of the image.  In many (most) cases, you can omit the `TAG` and the tag 'latest' will be assumed.  

In addition to the <font color="purple">reference</font>, each image will have a unique <font color="green">Image ID</font>, which acts in a similar fashion to the [container UUID](operating_systems/docker/docker_basics?id=identifying-containers). As a matter of fact, while all <font color="purple">references</font> that do _not_ have `<none>` as a repository _or_ tag will be unique, its entirely possible for _multiple_ images to exist with the same <font color="purple">reference</font> name and a `<none>` as a tag, and its also common for _multiple_ images to have _both_ repository _and_ tag as `<none>`; in these cases the image UUID becomes critical.  

> Where possible, try to _always_ have a legitimate <font color="purple">reference</font>.  

## Full Image Identification  

The `Repository` is the _minimum_ necessary to identify an image. The [reference](operating_systems/docker/image_commands?id=identifying-images) - which identifies the `Repository` and the `TAG` - are common identifiers. These two items are typically all that are required to identify both local images as well as major images on [hub.docker.com](https://hub.docker.com/search?type=image), but there are two more identifiers necessary to identify uncommon images: the domain of the registry that acts as the Docker repository, and the account owner of the image.  

For example, we could simply [pull](operating_systems/docker/image_commands?id=pulling-an-image) an ubuntu image with:  
```
docker pull ubuntu  
```  

However some assumptions are made above:  
* The Docker registry is either local _or_ `docker.io` (the defaults, in order, if you do not supply a registry).  
* The account owner ie either local _or_ `library` (the default, in order).  
* The tag `latest` (the default).  

In reality, the above is _actually_ pulling:  
```
docker pull docker.io/library/ubuntu:latest  
```  

---  

# Downloading Images  

Usually, you will be pasively downloading an image when you initially [attempt to run a container](operating_systems/docker/container_commands?id=running-containers) if you do not have it on the host, provided the image is on the [Docker site](https://hub.docker.com/search?type=image).  

There are other ways to get images, though - they will be detailed here.  

## Digest 

[Hashes](operating_systems/ubuntu/linux_notes?id=file-hashes) can be used to unuquely identify a file; more importantly, if you have the known hash of a file, you download it, and the hash is _not_ what you expect, you know the file is either corrupt _or_ some nefarious code was injected into the file as you were downloading it. 

Docker uses hashes to ensure that images you download are what they purport to be via their concept of a <font color="green">digest</font>: you provide the image name and the [sha256sum](operating_systems/ubuntu/linux_notes?id=file-hashes), and Docker will go and get the image and _then_ ensure that the sha256sums match.  

The first thing you must do is locate the image's sha256sum; for anything on [https://hub.docker.com](https://hub.docker.com), if you go to the image's page and then click the `Tags` tab, it will list all of the available tags for that image; under each `TAG` there will be a listed OS/ARCH type - pick the one that matches your host. Once you do, you will see a `Digest:sha256:` somewhere on the page - this is the sha256sum (for example, the current sha256 for the image:tag ubuntu:18.04 for OS/ARCH linux/amd64 is `6b26a5e25e3b4d0337997ab73f28c67427d0aa9ff65c0efc1996c576b238502c`); once I have this, I can [run a container](operating_systems/docker/container_commands?id=running-containers) to download the image using the <font color="green">digest</font> like so:  
```
docker run -dit ubuntu@sha256:6b26a5e25e3b4d0337997ab73f28c67427d0aa9ff65c0efc1996c576b238502c  
```  
* This will download the current version of Ubuntu 18.04 for amd64 and make sure the sha256sums match.  

!> Currently, using the <font color="green">digest</font> in such a manner is broken (see [this issue](https://github.com/moby/moby/issues/31656)); it does not properly add the tag to the image so the tag appears as `<none>`. You can update it if you download again using `IMAGE_REPOSITORY:TAG`: doing so will 'update' to the newest version and this will add the proper tag. This is not a great solution, but there is currently no other solution.  

## Pulling an Image  

Another way of obtaining an image is by directly pulling it _without_ [running a container](operating_systems/docker/container_commands?id=running-containers); this can be done like so:  
```
docker pull [IMAGE_NAME]:[TAG]
```  
* Do _not_ include the brackets `[]`.  
* You can find more `IMAGE_NAME` [here](operating_systems/docker/docker_basics?id=locations-of-pre-built-images).  
* The `:TAG` is not required; however, if you want a _specific_ version of the image this is how you specify it. More on tags [here](operating_systems/docker/image_commands?id=identifying-images).  
   * If you omit the `:TAG`, the image pulled will be the default image for the given `IMAGE_NAME` (usually its the tag `latest`).  
* You could also pull the image by swapping out the `TAG` with the [digest](operating_systems/docker/image_commands?id=digest) of the tag.  

## Updating An Image

You can update an image by simply [re-pulling it](operating_systems/docker/image_commands?id=pulling-an-image) again like so:  
```
docker pull [IMAGE_NAME]:[TAG]
```  
* Everything noted in [pulling an image](operating_systems/docker/image_commands?id=pulling-an-image) still applies.  

A **<font color="red">warning</font>** - if you try to update an image _and_ a new image is available, docker will remove the tag from the prevous tag of that name - simply leaving it tagless - and download a new image with the tag. At this point, you will have _two_ images of the repository, and one with a `<none>` tag which is undesirable.  There is no way to update the containers to use this new image (outside of Docker Compose), so you will have to manually update all containers!

---  

# Deleting Images  

To delete an image:  
```
docker image rm [IMAGE_NAME]
```  
* Do _not_ include the brackets `[]`.  
* This can also be the image's [long or short UUID](operating_systems/docker/container_commands?id=naming-a-container).  
* You can also include the `-f` tag to force delete the image, but be careful - this will render any existing container that uses this image inoperable.  

!> In order to delete an image, you _must_ make sure no containers are connected to this image. You can sort of check that with the [ancestor filter](operating_systems/docker/container_commands?id=filtering-listed-containers); if you do find containers that are connected, [stop](operating_systems/docker/container_commands?id=stop) them and then [remove](operating_systems/docker/container_commands?id=deleting-containers) said containers before removing the image.  

## Remove Dangling Images  

[Dangling images](operating_systems/docker/docker_basics?id=dangling-image) tend to take up space; occasionally you will need to prune these with:  
```
docker image prune 
```  
* This will remove any image with a repository of `<none>` _or_ a tag of `<none>`.  
* You could also use the `-f` paramter, which means 'force'; be careful with this though, as this will remove _all_ dangling images, even if they are associated with an active container (which will break the container).  

---  

# Show Images  

To show all of the images you have downloaded to your host:  
```
docker image ls
```  
* You can also use the `-a` flag if you would like to show the [intermediate images](operating_systems/docker/docker_basics?id=intermediate-image).  

## Filtering Listed Images  

Its possible to filter returned [images](operating_systems/docker/docker_basics?id=docker-image-term) with the `--filter` (or `-f`) parameter. Every filter needs _at least_ one condition listed immediately after the flag; the possible conditions are:  

| Condition | Values | Description | Example |  
| --- | --- | --- | --- |  
| before | | Shows a list of images created before the listed image. | `docker image ls -af before=[IMAGE_NAME]` |  
| label | | Search for a specific [label](operating_systems/docker/docker_basics?id=label) (either they key or both key/value) across the given images. There are some caveats to labels in images; see [here](operating_systems/docker/image_commands?id=labels-in-images) for mroe information. | See [here](operating_systems/docker/image_commands?id=labels-in-images). |  
| since | | Shows a list of images created after the listed image. | `docker image ls -af since=[IMAGE_NAME]` |  
| dangling | (true, false) | Shows a list of containers that are either a [dangling image](operating_systems/docker/docker_basics?id=dangling-image) or not.| `docker image ls -af dangling=true` |  
| reference | | Search the images using the [reference](operating_systems/docker/image_commands?id=identifying-images). | `docker image ls -f reference=ubuntu:21.10` |  
* If you see `[IMAGE_NAME]` above, do _not_ include the brackets `[]`.  


## Formatting a Search  

Its possible to format the results of a search with the `--format` option. Formatting is nice, as you get to pick the fields; in addition, there are several fields that are _only_ available with a format and not available in the vanilla search. Formats use a Go template to achieve its goal.  

The available fields are:  

| Field | Description |  
| --- | --- |  
| .ID | Image ID. |  
| .Repository | Image [repository name](operating_systems/docker/image_commands?id=identifying-images). |  
| .Tag | Image [tag](operating_systems/docker/image_commands?id=identifying-images). |  
| .Digest | Shows the sha256 [digest](operating_systems/docker/image_commands?id=digest). |  
| .CreatedSince | Elapsed time since the image was created. |  
| .CreatedAt | Time when the image was created. |  
| .Size | Image disk size. |  
| .Labels | All [labels](operating_systems/docker/container_commands?id=labels) assigned to the container. |  
| .Label | Value of a specific label for this container). See below for an individual example. |  


An example:  
```
docker image ls -a --format 'table {{.Repository}}\t{{.Tag}}\t{{.Digest}}'
```  
* Note the _entire_ thing is wrapped in single quotes. You could also use double quotes, but I used them to identify the unique label.  
* Each selection is wrapped in `{{}}`.  
* The word 'table' prints the headers of the columns; you can leave this off if you wish, but I prefer it.  
* You can use a `\t` (tab) for nice spacing.  

## Searching By Image Name  

To search for a _specific_ image you can do the following:  
```
docker image ls [IMAGE_NAME]:[TAG]
```  
* Do _not_ include the brackets `[]`.  
* You can find more `IMAGE_NAME` [here](operating_systems/docker/docker_basics?id=locations-of-pre-built-images).  
* The `:TAG` is not required; however, if you want a _specific_ version of the image this is how you specify it. More on tags [here](operating_systems/docker/image_commands?id=identifying-images).  
* You could _also_ search by the image's [short UUID](operating_systems/docker/docker_basics?id=identifying-containers).  

## Show Only Short UUIDs  

Sometimes its best to get a list of identifiers quickly - to do that, use `-q`: 
```
docker image ls -q
``` 

---  

# Labels in Images  

[Labels](operating_systems/docker/docker_basics?id=label) do exist in images, but things are not as streamlined as they are in containers - they do _not_ display with `docker image ls` _at all_ - not even with a format.  

You _can_ at least search for labels that exist (even if you cannot see the value); for example, you can find all images that have the label `org.label-schema.schema-version` like so:  
```
docker image ls -f label=org.label-schema.schema-version  
```  

From there, you could dig into the inspect with `docker inspect IMAGE_NAME` but this is a round-about way of doing this.  

Speaking of `org.label-schema.schema-version`, that is a part of a project that hopes to standardize a few basic labels for images (their website is [http://label-schema.org](http://label-schema.org/rc1/); a few of the notable ones are:
* org.label-schema.build-date  
* org.label-schema.license  
* org.label-schema.name  
* org.label-schema.schema-version  
* org.label-schema.vendor  

Note that these are _not_ guaranteed in all images.  In addition, the page [http://label-schema.org](http://label-schema.org/rc1/) says this schema is deprecated in favor of [OCI IMAGE SPEC](https://github.com/opencontainers/image-spec), but feel free to pick one.  

---  

# Creating Images - Dockerfile  

> The man page for this is `man docker-image-build`  

At some point, very early on in your Docker experience, you _will_ need to write a script to build a _custom_ Docker image; its inevitable. You _will_ need an image that has unique environment variables, specific apps, specific directories, specific uers, etc; and if you are using Docker to deploy personal / corporate apps, these apps will be updated frequently - so you will need to develop a new Docker image to support this frequently.  

You can create your own image via creating a [Dockerfile](operating_systems/docker/dockerfile). Once you have created said Dockerfile, you can then run the command `docker image build` like so:  
```
docker image build --tag [IMAGE_NAME]:[TAG] .
```  
* Do _not_ include the brackets `[]`.  
* You get to define the [IMAGE_NAME](operating_systems/docker/docker_basics?id=docker-image-term) and [TAG](operating_systems/docker/image_commands?id=identifying-images) using the `--tag` parameter.  
   * While its not required to give an `IMAGE_NAME` via the `--tag` (or optionally `-t`) parameter, I strongly recommend it - otherwise, Docker will simply leave the Repository as 'none' _and_ the tag as 'none' - the only way you can identify the image after that is with the image ID.  
   * To avoid confusion, make sure the `IMAGE_NAME` is _not_ a common one found [here](https://hub.docker.com/search?type=image).  
   * You can leave the `:[TAG]` portion off the command and Docker will simply give it the `latest` tag.  
* The `.` simply references the working directory of the Dockerfile, as well as all other referenced files.  
   * In this case, the Dockerfile is in the cirrent directory.  
   * This could be an absolute path _or_ it could be relative to your current working directory.  
   
!> Be **very** careful about running this command with a specific [reference](operating_systems/docker/image_commands?id=identifying-images) more than once (with changes to the Dockerfile or associated files; if you make no change and run it, it will not actually replace the image). Every time you run this (with changes), the old run will become an [intermediate image](operating_systems/docker/docker_basics?id=intermediate-image) which can still be tied to a container but will have its repository name / tag nulled out (so you cannot use them to reference that image any more). Ths is fine if you are building it as a test (as you can just [rune the old images](operating_systems/docker/image_commands?id=remove-dangling-images) easily), but if you are building this to immediately launch a container, be _careful_ of either updating the tag or quickly deleting the test container you create.
   
## Nonstandard Dockerfile Names  

If you do not wish to use the standard [Dockerfile name](operating_systems/docker/dockerfile?id=basic-dockerfile-example), you _can_ use a file that is named whatever you wish _if_ you use the `--file` (or `-f`) parameter like so:  
```
docker image build --tag [IMAGE_NAME]:[TAG] --file=[FILE_NAME] .
```  
* Do _not_ include the brackets `[]`.  
* You do _not_ have to include the location of `FILE_NAME`, as the location is taken care of with `.` (or the working directory set).  

## Overwriting Arguments  

Its possible - and common - to overwrite [ARG](operating_systems/docker/dockerfile?id=arg-setting-internal-arguments) parameters set in the Dockerfile.  This is done with the `--build-arg` parameter:  
```
docker image build --tag [IMAGE_NAME]:[TAG] --build-arg=SOME_OTHER_DIR=test_file/text .
```  
* Do _not_ include the brackets `[]`.  
* This is following [this example](operating_systems/docker/dockerfile?id=basic-dockerfile-example).  
   * Note the `ARG` <font color="purple">SOME_OTHER_DIR</font> - we are replacing the default with `test_file/text` above.  

> Overwriting arguments is common in Docker.  

## Adding Labels (Image Build)  

You can add _additional_ labels to the image (or override a label set in a [LABEL instruction](operating_systems/docker/dockerfile?id=label-setting-image-labels)) with the `--label` parameter:  
```
docker image build --tag [IMAGE_NAME]:[TAG] --label=maintainer="Bob (bob@vandelayindustries.com" .
```  
* Do _not_ include the brackets `[]`.  
* This is following [this example](operating_systems/docker/dockerfile?id=basic-dockerfile-example).  
   * This will override the `maintainer` label set in the LABEL instruction but will not remove or alter the other labels present.  

Again, note that using `--label` will not remove any label as set in a [LABEL instruction](operating_systems/docker/dockerfile?id=label-setting-image-labels) unless you are specifically overriding it; otherwise, it will simply add the label.  

## Other Image Build Options  

There are a ton of other options for the image - ones that set CPU shares, ones that deal with memory, etc. I am not getting into these here but will at least list them from the man page (`man docker-image-build`):  

| Option | Description |  
| --- | --- |  
| --add-host | Add a custom host-to-IP mapping (host:ip) |  
| --cache-from | Images to consider as cache sources |  
| --cgroup-parent | Optional parent cgroup for the container |  
| --compress | Compress the build context using gzip |  
| --cpu-period | Limit the CPU CFS (Completely Fair Scheduler) period |  
| --cpu-quota | Limit the CPU CFS (Completely Fair Scheduler) quota |  
| -c, --cpu-shares | CPU shares (relative weight) |  
| --cpuset-cpus | CPUs in which to allow execution (0-3, 0,1) |  
| --cpuset-mems | MEMs in which to allow execution (0-3, 0,1) |  
| --disable-content-trust | Skip image verification |  
| --force-rm | Always remove intermediate containers |  
| -h, --help | help for build |  
| --iidfile | Write the image ID to the file |  
| --isolation | Container isolation technology |  
| -m, --memory | Memory limit |  
| --memory-swap | Swap limit equal to memory plus swap: '-1' to enable unlimited swap |  
| --network | Set the networking mode for the  RUN  instructions during build |  
| --no-cache | Do not use cache when building the image |  
| -o, --output | Output destination (format: type=local,dest=path) |  
| --platform | Set platform if server is multi-platform capable |  
| --progress | Set type of progress output (auto, plain, tty). Use plain to show container output |  
| --pull | Always attempt to pull a newer version of the image |  
| -q, --quiet | Suppress the build output and print image ID on success |  
| --rm | Remove intermediate containers after a successful build |  
| --secret | Secret file to expose to the build (only if BuildKit enabled): id=mysecret,src=/local/secret |  
| --security-opt | Security options |  
| --shm-size | Size of /dev/shm |  
| --squash | Squash newly built layers into a single new layer |  
| --ssh | SSH agent socket or keys to expose to the build (only if BuildKit enabled) |  
| --target | Set the target build stage to build. |  
| --ulimit | Ulimit options |  

---  

# Creating Images From a Container   

Its possible that you have a running [container](operating_systems/docker/docker_basics?id=container), you have manually installed a bunch of helpful tools / set the configuration just right, and you wish to make an [image](operating_systems/docker/docker_basics?id=docker-image-term) out of the container so you can use it elsewhere. It is absolutely possible to do this with `docker container commit`:  
```
docker container commit [EXISTING_CONTAINER_NAME] [NEW_IMAGE_NAME]:[TAG]
```
* Do _not_ include the brackets `[]`.  
* The `EXISTING_CONTAINER_NAME` is the name of the existing container you wish to turn into an image.  
* The `NEW_IMAGE_NAME` is the name you want to give the new [image](operating_systems/docker/docker_basics?id=docker-image-term).  
* The `:TAG` is not required; however, if you omit the `:TAG`, your new image will have a default tag of `latest`.  

---  

# Inspecting Images  

Inspecting images will give you a good deal of information about the image itself; to inspect an image:  
```
docker inspect [IMAGE_NAME]:[TAG]
```  
* Do _not_ include the brackets `[]`.  
* You can find more `IMAGE_NAME` [here](operating_systems/docker/docker_basics?id=locations-of-pre-built-images).  
* The `:TAG` is not required; however, if you want a _specific_ version of the image this is how you specify it. More on tags [here](operating_systems/docker/image_commands?id=identifying-images).  
* You could _also_ search by the image's [short UUID](operating_systems/docker/docker_basics?id=identifying-containers).  


