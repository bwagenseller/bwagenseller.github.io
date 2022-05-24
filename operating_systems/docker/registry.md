# Docker Registries  

# Basics  

This document will show the basics of using a <font color="green">Docker Registry</font>.  

Every [image](operating_systems/docker/docker_basics?id=docker-image-term) you use, sometime in the chain of images, almost assuredly came from a <font color="green">Docker Registry</font>; as a matter of fact, the <font color="green">Docker Registry</font> is a part of the [long form of an image name](operating_systems/docker/image_commands?id=full-image-identification).  The default registry is docker.io (or [the Docker hub](https://hub.docker.com/search?q=&type=image) - for most implementations of Docker, you will never use any other <font color="green">Docker Registry</font> than this one, but you may at some point.  

While you may never use a different registry, you _may_ have to use some images that are _not_ standard-issue images (or even images you create and store on docker.io) - using such registries will also be showcased in this document.  

## Register at Docker.io

Go [here](https://hub.docker.com/signup) to make a free Docker account; this will be necessary for the examples here.  

## Logging In  

Before you run the `login` command, you <font color="red">are required to</font>:  
1\. [Register an account on docker.io](operating_systems/docker/registry?id=register-at-dockerio).  
2\. Have at least one [GPG private/public key pair](operating_systems/ubuntu/linux_notes?id=gpg) on your local machine.  
3\. Have your password store initialized with your [GPG fingerprint](operating_systems/ubuntu/linux_notes?id=identifying-gpg-keys) a la `pass init YOUR_GPG_FINGERPRINT_ID_HERE`  
   * You may have to edit your GPG user and [set the trust to 5](operating_systems/ubuntu/linux_notes?id=editing-gpg-keys) (which is 'ultimate') - otherwise you may get errors like `gpg: There is no assurance this key belongs to the named user`.  


After you have completed the above, log in to docker.io via the command line:  
```
docker login --username [USER-NAME]
```
* Do _not_ include the brackets `[]`.  
* The `USER_NAME` is your username you [registered with](operating_systems/docker/registry?id=register-at-dockerio).  
* After you run this, you will be prompted for your docker.io password.  

If succssful, you will see `Login Succeeded`.  

You may _also_ see this:

> WARNING! Your password will be stored unencrypted in /root/.docker/config.json.  
Configure a credential helper to remove this warning. See  
https://docs.docker.com/engine/reference/commandline/login/#credentials-store  

Ignore this for now if you do not wish to install credentials-store ( it is more secure, but I find it touchy to use).  

## Logging Out  

To log out, type:  
```
docker logout  
```  

# Pushing to a Registry 

To push a remote registry can be as simple as issuing a `docker push`, but there is more to it than that.  

First and foremost, you _must_ have an account on a registry somewhere. Most likely you will use the standard one from Docker, so [register an account](https://hub.docker.com/signup) at hub.docker.com and note your user ID (for our purposes we will say the userID is <font color="red">uperson</font>). Also, **make sure to validate the email address** using the link Docker sends you.  

Secondly, you must [login](operating_systems/docker/registry?id=logging-in).  

After that, you must make sure your image is prepped properly - by that, I mean your userID (i.e. the account owner) _must_ be somewhere in the name of the image (recall the [full image identification](operating_systems/docker/image_commands?id=full-image-identification). Under normal circumstances, when you [create an image](operating_systems/docker/image_commands?id=creating-images-dockerfile) you typically only use the [reference](operating_systems/docker/image_commands?id=identifying-images) (i.e. `REPOSITORY:TAG`) when naming the image. If you wish to store the image on a remote registry, though, you _must_ include your userID as well. For example, if your repository name was `someimage:1.0`, and you wanted to store this on a remote registry, you would have to name it <font color="red">uperson</font>`/someimage:1.0`.  

You can outright name your image like this, and you will be able to push it just fine; if you named your image without the userID though, you do have another option: `tag`. You can make an additional `tag` of the image (tag is a misnomer here, its not like the tag in a reference, this seems to make a copy of sorts of the original image).  The syntax for this particular type of tagging is:  
```
docker tag NAME_OF_IMAGE_ON_YOUR_LOCAL_HOST:TAG_ON_LOCAL_HOST uperson/NAME_OF_IMAGE_ON_REPOSITORY:TAG_ON_REPOSITORY
```  
* The userID <font color="red">uperson</font> is your login on the registry.  
* Note that `NAME_OF_IMAGE_ON_YOUR_LOCAL_HOST` does _not_ have to be the same as `NAME_OF_IMAGE_ON_REPOSITORY`; this is also true for `TAG_ON_LOCAL_HOST` and `TAG_ON_REPOSITORY`.  

Once you have the image in `uperson/REPOSITORY:TAG` format - via outright naming the image like this _or_ by using a tag (as explained above) - you will be ready to push your image to the remote registry. To do so:  
```
docker push uperson/REPOSITORY:TAG
```  

If successful, you will see a [digest](operating_systems/docker/image_commands?id=digest) returned; for example:  
```
1.0: digest: sha256:1579672178401686d31cc2a97115740c7e5fc55e6b8308730e3e179637f22a4b size: 946
```  
* The `1.0` refers to the tag.  

## Issues With Pushing  

A common error received when pushing is `requested access to the resource is denied`; this can be due to a few reasons:  
* You are not [logged in](operating_systems/docker/registry?id=logging-in).  
* You did not click on the verification email link sent from Docker when you initially signed up for the account.  
* You did not properly use the [userID in the image name](operating_systems/docker/registry?id=pushing-a-repository) (if you do not, Docker assumes the default of `library` and will say something like `The push refers to repository [docker.io/library/YOUR_IMAGE_NAME_HERE]` and chances are you do not have rights to push to `library`).  

If you are using Docker's credentials-store there could also be an issue with that. I did try the credentials store but it gave me massive problems - every time I tried to push my image I got this:  
```
07616f77b384: Preparing 
ac794a8f5add: Preparing 
4fc242d58285: Preparing 
denied: requested access to the resource is denied
```  

If you have the credentials-store installed and are having issues, try uninstalling credentials-store (I had to uninstall _all_ of Docker) to get back to the basics. I may circle back to the credentials-store later, but for now I just didnt use it.  

# Access Tokens  

Its possible to use an <font color="green">access token</font> instead of a password when [logging in](operating_systems/docker/registry?id=logging-in) to a registry (so instead of just typing in a password, you would type in your assigned <font color="green">access token</font>). This is moreso useful for teams of people that need multiple people accessing a single account - you can use the same account for everyone whle giving them all a different <font color="green">access token</font>, and if someone leaves the company, just revoke their token.  

To manage your tokens, go to [https://hub.docker.com/settings/security](https://hub.docker.com/settings/security) (while logged into the site) and you will see the options to manage your <font color="green">access tokens</font>.  

> When I created mine, the _only_ time I was able to actually see the token itself was when I created it - after that, I can only see the label of the token but cannot recover the actual token. It would probably be easy enough to just delete the token and re-generate one, but note this.  

# Setting Up a Registry  

> I want to expand on setting up your _won_ registry later (usually via docker-compose) - for now, [please follow along at gabrieltanner.org](https://gabrieltanner.org/blog/docker-registry). Also chapter 5 in "Learn Docker In a Month of Lunches" has some good tips as well, but that uses its own pre-built image to do this and I wanted to avoid that; that said, there are other good bits of information in there.   

