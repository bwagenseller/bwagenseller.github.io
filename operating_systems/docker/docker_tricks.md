# Docker Tricks  

# Purpose  

The purpose of this file is to list various tips / tricks with Docker that do not quite fit in anywhere else.  

# Must Be Run As Root

This must be stated - almost all commands _must_ be run as root! There is a way to get around this, but there seems to be constant problems with it (the one I ran into revolved around [/var/run/docker.sock](https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue) which _requires_ you set this file's [chmod](operating_systems/ubuntu/linux_notes?id=changing-permissions) to `666` for every re-set, as well as [adding the user](operating_systems/ubuntu/linux_notes?id=adding-users-to-groups) to the `docker` group). Because of this, I run all commands as root.   

---  

# Abusing -q Flag  

Most entity commands in Docker have a `ls` command (`docker container ls`, `docker image ls`, etc), most have a `-q` flag, and most have a `--fiter` as well. You can combine these to return _just_ the list of [short UUIDs](operating_systems/docker/docker_basics?id=identifying-containers) of the element in question; then, you can pipe those UUIDs into other Docker commands.  

For example, if you wanted to stop _all_ containers, you could use this trick like so:  
```
docker container stop $(docker container ls -aq)
```  
* The `docker container ls -aq` returns all container short UUIDs.  
   * You could _also_ do this with a format a la 
```
docker container ls -a --format '{{.ID}}'
```  
   Furthermore, you could also use the name (`.Names`) instead of `.ID` if you were forced to use names.  
* The `$()` feeds the short UUIDs as values into whatever accepts it.  
* The `docker container stop` accepts those short UUIDs and stops all of them.  

You can do this with many, many things - just remember to keep the `ls` output to only ONE item type, either with a `-q` _or_ a format.  


