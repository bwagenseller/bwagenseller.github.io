# Akka Install And Configuration

# What is Akka?

Akka is an implementation of the [Actor Model](learn_to_code/java/akka/akka_basics?id=actor-model), which is itself a concepts that competes with threading. Akka's main advantage is that it eliminates the conceopf of shared state - so there is no need to worry about locking shared variables as you would have to using threads. 

In addition, Akka utilizes fault-tolerant distribution - it does this with its use of [supervision](learn_to_code/java/akka/akka_basics?id=supervision) in as well as through its support of running in a distributed setting (so multiple networked machine can work together to accomplish tasks); in short, Akka scales well and can maintain high availability via [supervision](learn_to_code/java/akka/akka_basics?id=supervision).

# Installation

Most of the installs revolve around <font color="purple">Typesafe Activator</font>, which is a bundle that contains a bunch of things such as Scala, Akka, Play, and Simple Build Tool (SBT); that said, I prefer the [Maven](learn_to_code/java/akka/akka_installation_and_config_files?id=maven-no-install-necessary) approach as nothing else is really needed (outside of Maven and the configuration of its POM file, that is).

## Windows Typesafe Activator Install

Download Typesafe Activator from [here](http://www.typesafe.com/get-started).

## Ubuntu Typesafe Activator Install

1\. Download Typesafe Activator from [here](http://www.typesafe.com/activator/download), and if that does not work, try [here](http://www.typesafe.com/get-started).

2\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

3\. Determine where you would like to open the zip file - a good choice would be `/opt/activator`. If this does not exist, create it, then move the `.zip` file to this folder and use the [unzip command](operating_systems/ubuntu/linux_notes?id=unzip-extracting-files) to unzip it.

> `unzip` keeps the structure of its folders, so it will create any folders inside of it. I am not sure if the Typesafe Activator `.zip` file has an immediate subdirectory (with nothing else on the first level); if this is the case, you can probably just store the `.zip` file in `/opt` and then [unzip](operating_systems/ubuntu/linux_notes?id=unzip-extracting-files) it from there; these instructions will assume the base path is `opt/activator` but if it is not, you will have to slightly modify the instructions from here on out for the install.

4\. Make the Activator executable:
```
chmod 755 /opt/activator/activator
```

5\. Add the Activator to (everyone's) path by editing the bottom of `/etc/profile` (we are using [VI](operating_systems/ubuntu/linux_notes?id=using-vi), but if you are not comfortable with VI use another text editor):
```
vi /etc/profile
```

> If you wish to edit only a single user's profile, use `/home/username/.profile` in place of `/etc/profile` here (but switch the `username` with the actual username).

6\. Paste this at the bottom of the file:
```
PATH=/opt/activator:$PATH 
export PATH
```

7\. Save and exit VI: `wq!`

8\. Lout out of root: `exit`

9\. Source in the new profile: `source /etc/profile`

> If you chose a user's personal path in step #5 AND its the current user, this will be `source ~/.profile`

# Maven (No Install Necessary)

> The master Akka Maven repository can be found [here](https://mvnrepository.com/artifact/com.typesafe.akka).  

!> I use [Maven](learn_to_code/java/maven) for all of my Java projects (including my Akka examples). In order for you to run any examples I have, you will have to understand a bit about [Maven](learn_to_code/java/maven), how to [compile a Maven project](learn_to_code/java/maven?id=compiling-your-maven-project), and [its directory structure](learn_to_code/java/maven?id=locations-of-files-in-maven). If you do not know what Maven is, [here is a brief overview](learn_to_code/java/maven?id=what-is-the-main-idea-behind-maven).

The nice thing about [Maven](learn_to_code/java/maven) is it can bundle all necessary jar files for your project - including Akka - where the only installs required are [Maven](learn_to_code/java/maven?id=ubuntu-install) and [Java](operating_systems/ubuntu/server_build?id=installing-java-from-oracle) itself.

## Scala Versions

You do not need Scala install in order to work with Akka, but portions of Scala are bundled in with Akka in the Maven repositories. Currently (as of Akka version 2.6.4), the Scala version is in the Maven `artifactId` - the format is `akkaArtifact_ScalaVersion`; for example, the main Akka artifact - `akka-actor` - is currently either `<artifactId>akka-actor_2.12</artifactId>` or `<artifactId>akka-actor_2.13</artifactId>` (representing Scala versions 2.12 and 2.13).  _If you have a mix of Scala versions in your Akka artifacts you will most likely have problems in the code - STAY CONSISTENT._  


## Using Maven Properties  

Akka will usually require 4+ dependencies in Maven, and each one will need both an Akka version _and_ a Scala version; they usually must all be the same. Its best to use Maven's ability to [set Maven variables / properties](learn_to_code/java/maven?id=adding-variables-properties) so if you ever wish to alter the Akka and / or the Scala version in Akka, you only have to do it in one place instead of multiple places.  

An example properties section that would be useful for Akka would be:

```
    <properties>
        <akka.version>2.6.4</akka.version>
        <akka.scala.version>2.13</akka.scala.version> 
    </properties>
```  

Then, you could reference Akka dependencies as follows:

```
        <dependency>
            <groupId>com.typesafe.akka</groupId>
            <artifactId>akka-actor-typed_${akka.scala.version}</artifactId>
            <version>${akka.version}</version>
        </dependency>
```  

## Basic Akka Maven Artifacts  

To install Akka to [Maven](learn_to_code/java/maven), use [these dependency entries in Maven](learn_to_code/java/maven?id=adding-dependencies-to-maven) to add the basic dependencies Akka uses (note: this uses the [Maven properties defined here](learn_to_code/java/akka/akka_installation_and_config_files?id=using-maven-properties):

```
        <dependency>
          <groupId>com.typesafe.akka</groupId>
          <artifactId>akka-actor_${akka.scala.version}</artifactId>
          <version>${akka.version}</version>
        </dependency>

        <dependency>
          <groupId>com.typesafe.akka</groupId>
          <artifactId>akka-slf4j_${akka.scala.version}</artifactId>
          <version>${akka.version}</version>
        </dependency>		
			
```

!> If using IntelliJ, IntelliJ can have some trouble recognizing imported projects. If you import the project then run a `mvn clean install`, the libraries exist but may not be recognized by IntelliJ. To reimport, right-clicking the project and then `Maven -> Reimport`.


# Application.conf  

> For more on this file, see the [Akka documentation on configuration](https://doc.akka.io/docs/akka/current/general/configuration.html). You do not need `application.conf`, but if you want to tweak Akka, you will need the file.  

The file `application.conf` is an Akka configuration file. The file should be in the root of your [jar file](learn_to_code/java/java_basics?id=jar-files), but if you are using [Maven](learn_to_code/java/maven), you can place the file in the [recources folder](learn_to_code/java/maven?id=resources-in-maven) (which is usually `src/main/resources`) and it will automatically be placed in the root of the jar file when compiled with Maven.  

You can place known (i.e. pre-defined variables that Akka uses to set various things) or custom variables here (and access them later); Akka uses a hierarchy with dot notation, and you are allowed to use dot notation, curly braces, or a combination of both. For example, lets say you wanted to set the known variable `akka.actor.warn-about-java-serializer-usage = off`; you could set it in one line in `application.conf` a la:  
```
akka.actor.warn-about-java-serializer-usage = off
```  

_OR_ you could fully use curly braces:  
```
akka { 
  actor { 
    warn-about-java-serializer-usage = off
  }
}
```  

_OR_ you could use a combination:  
```
akka.actor { 
  warn-about-java-serializer-usage = off
}
```  

!> Using `akka.actor.allow-java-serialization = on` is not recommended due to security issues, so `akka.actor.warn-about-java-serializer-usage = off` is usually not used.  

> I will not go into detail here about different settings, but I will mention some basic ones here and throughout my documentation.  