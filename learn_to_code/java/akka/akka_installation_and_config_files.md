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

# SBT

> These instructions initially found on [techrepublic](https://www.techrepublic.com/article/how-to-install-sbt-on-ubuntu-for-scala-and-java-projects/).  

SBT is the build tool used in Scala; Lightbend also uses it for its Java applications. I usually do not use SBT, but sometimes its necessary. Unfortunately, there is no direct way to install SBT on Ubuntu, so you will ahve to do the following:  
1\. type: `echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list`  
2\. Add the public key for the installation with the command:  
`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823`  
3\. Update: `sudo apt-get update`  
4\. Install sbt with the command: `sudo apt-get install sbt -y`  
5\. To test, run: `sbt test`  

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

## Including Files  

Its possible to include files in your `application.conf` - this means you can place additional parameters in another file and it will be loaded when `application.conf` is loaded.  An example line in `application.conf` that reads a file for parameters:  
```
include file("/etc/SomeDirectory/SomeConfFile.conf")
```  

It is important to note that <font color="red">the placement of this include is important</font>.  If a value is set in the include file statement at the top of `application.conf` and then set _again_ in `application.conf` itself, the version in `application.conf` is kept; if reversed, the value of whatever is in the imported file is kept. The last value seen is the value kept (which is unlike the [ConfigFactory object](learn_to_code/java/akka/akka_installation_and_config_files?id=loading-options)).  

> Using `include file` can be helpful if you want to make moving a jar file from, say, a development server to a production server easy - have the `application.conf` file reference an external conf file (if it doesnt exist it just quietly fails). This can load environment-specific parameters easily while leaving the core parameters up to the jar file. Just note that the `include file` should be included _at the end_ of `application.conf` due to the nature of overwriting parameters (mentioned directly above).  

---  

# Loading Options  

> More can be read [here](https://dzone.com/articles/typesafe-config-features-and-example-usage).  

Loading [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf) is easy - just put it in the root of your jar file and it will automatically load and be used to configure Akka.  That said, what if you need to: 
* Load additional data?  
* Override settings based on the environment (development server, test server, production server)?  
* Override settings for unit tests?  

There are many reasons as to why you may want to load a different configuration file - fortunately, Akka provides this ability with the <font color="purple">Config</font> and <font color="purple">ConfigFactory</font> classes.  

## Importing Akka Config Classes

To import the needed classes, simply import this:  
```
import com.typesafe.config.*;
```  

Although if you wanted to specify the exact classes you can do so like this:  
```
import com.typesafe.config.Config;
import com.typesafe.config.ConfigFactory;
```  

## Example Config File

This is an example config file I use. Note that you can specify _any_ parameter here whether or not it has a use (clearly, `brent.wag.haircolor` is not a used parameter normally), _so long_ as it follows [the curly brace or dot notation](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf).

The file is `yonkered.conf`:  
```
brent {
  wag {
    haircolor = brown
    height = 73
  }
}
akka {
  cluster {
    seed-nodes = [
	 "akka://VandelayIndustries@127.0.0.1:25520",
	 "akka://VandelayIndustries@127.0.0.1:25521",
	 "akka://VandelayIndustries@127.0.0.1:25522"
	]
  }	
}
```  
* This also assumes you have an [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf) with `akka.remote.artery.canonical.port` defined and set as an integer).  

## Basic Akka Config  

The <font color="purple">Config</font> object type is the base type that Akka uses as a parameter to build the [ActorSystem](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation).  If you do not specify the config, Akka will automatically read in and use [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf) _if_ its available, and if its not, it will use all default values. This is an example of using [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf) _or_ all defaults if [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf) does not exist :  
```
final ActorSystem system = ActorSystem.create("helloakka");
```  

You can accomplish the _same_ result with `ConfigFactory.load()` like so:  
```
final ActorSystem system = ActorSystem.create("helloakka", ConfigFactory.load());
```  

However, if you have a different config file you wish to load instead you can use the method `parseResources()`. Below I use my [example external config file](learn_to_code/java/akka/akka_installation_and_config_files?id=example-config-file); put it in the root of your [jar file](learn_to_code/java/java_basics?id=jar-files) or in the in the [recources folder](learn_to_code/java/maven?id=resources-in-maven) if you are using [Maven](learn_to_code/java/maven):  
```
final ActorSystem system = ActorSystem.create("helloakka", ConfigFactory.parseResources("yonkered.conf"));
```  

You could do the same thing by splitting the statements up using a `Config` object:  
```
Config myConfig = ConfigFactory.parseResources("yonkered.conf");
final ActorSystem system = ActorSystem.create("helloakka", myConfig);
```  
## On The Fly Config Strings  

Its possible to provide on the fly configurations with the method `parseString()`.  This bypasses `application.conf` entirely (we will show how you can use both in a bit):  
```
Config myConfig = ConfigFactory.parseString("brent{wag{state = Pennsylvania \n county = Montgomery}} \n akka.remote.artery.canonical.port = 19345");
final ActorSystem system = ActorSystem.create("helloakka", myConfig);
```  
* Note that this bypasses `application.conf` entirely (I will show how you can use both in the section [chaining configurations](learn_to_code/java/akka/akka_installation_and_config_files?id=chaining-configurations)).  
* The newline character `\n` is critical here if you wish to set multiple parameters - it should go everywhere you would notmally press the `Return` key.  
* Note I used bot hthe brace notation _and_ the dot notation - either are acceptable.  

## Chaining Configurations  

It is often desirable to use the contents of a base file and supplement it with parameters from - or even override parameters from - another configuration file.  This is possible via chaining the configuration, which lists the sources from 'most important' to least important' source.  

For example, lets say you want to load [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf) _and_ the parameters in [yonkered.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=example-config-file); lets also say you want the parameters in `yonkered.conf` to override all parameters in `application.conf` _if_ there is a conflict.  We can do this with listing `yonkered.conf` _first_, _then_ listing the default configuration file; we do this with using the `withFallback()` method:  
```
Config leastImportantConfig = ConfigFactory.parseResources("SomeConfFileThatDoesntExistBecauseIDidntFeelLikeActuallyCreatingIt.conf");
Config lessImportantConfig = ConfigFactory.load();
Config myConfig = ConfigFactory.parseResources("yonkered.conf").withFallback(lessImportantConfig).withFallback(leastImportantConfig);
final ActorSystem system = ActorSystem.create("helloakka", myConfig);
```  
* In this example, the parameters from `yonkered.conf` will override - and be complimented with - parameters from `lessImportantConfig`, which in turn will override - and be complimented with - parameters from `leastImportantConfig`.  
* `withFallback()` takes a `Config` object, _not_ a `String`.  
* `withFallback()` can be chained indefinitely.  

Often - particularly in unit testing - you will want to override a host or port and keep everything else the same; you can do this with getting a `Config` object from an [on the fly config string](learn_to_code/java/akka/akka_installation_and_config_files?id=on-the-fly-config-strings) and _then_ loading the regular `application.conf`; to do so:  
```
Config unitTestConfig = ConfigFactory.parseString("akka.remote.artery.canonical.hostname = \"127.0.0.1\" \n akka.remote.artery.canonical.port = 19525");
Config normalConfig = ConfigFactory.load();
Config myConfig = unitTestConfig.withFallback(normalConfig);
final ActorSystem system = ActorSystem.create("helloakka", myConfig);
```  

## Config Variables

Its possible to use variables in a config file with `${}`. We can set some variable using another variable with this; for example:  
```
Config unitTestConfig = ConfigFactory.parseString("akka.remote.artery.canonical.hostname = \"127.0.0.1\" \n akka.remote.artery.canonical.port = 19525 \n wag = ${akka.remote.artery.transport}");
Config normalConfig = ConfigFactory.load();
Config myConfig = unitTestConfig.withFallback(normalConfig).resolve();
final ActorSystem system = ActorSystem.create("helloakka", myConfig);
```  
* **NOTE**: The above assumes you have `application.conf` and `akka.remote.artery.transport` is set in that file.  
* `wag` is set to `${akka.remote.artery.transport}`, which means the _value of_ `akka.remote.artery.transport`.  
 * I had `akka.remote.artery.transport` in `application.conf` set to `tcp`, so `wag` was _also_ set to  `tcp`.  
* The `.resolve()` is _critical_ when using variables.  

> It is important to note that you use `.resolve()` when using variables - this figures out the value it should be. It is important to do this for the _final_ `Config` variable, as otherwise the variable me resolve to something that is not intended (or throw an outright error as it was not resolved properly).  

## Determine If Param Exists

To determine if a parameter exists, use `hasPath()`; using my [example](learn_to_code/java/akka/akka_installation_and_config_files?id=example-config-file):  
```
Config myConfig = ConfigFactory.parseResources("yonkered.conf");
System.out.println(myConfig.hasPath("akka.cluster.seed-nodes"));
System.out.println(myConfig.hasPath("brent.wag.weight"));
System.out.println(myConfig.hasPath("brent.wag.height"));
```  
* The `seed-nodes` and `height` will return `true`, but my weight will return `false` as I didnt list it in the config file.  

## Reading Variables From Conf

Its possible to read variables from the configuration using `getString()`.  For example:  
```
Config unitTestConfig = ConfigFactory.parseString("akka.remote.artery.canonical.hostname = \"127.0.0.1\" \n akka.remote.artery.canonical.port = 19525");
System.out.println(myConfig.getString("akka.remote.artery.canonical.hostname"));
```
* This would print `127.0.0.1`.  

!> To my knowledge there is no way to _set_ variables once they have been set initially! Your best bet would be to [override the setting on the fly](learn_to_code/java/akka/akka_installation_and_config_files?id=on-the-fly-config-strings) and then load in the rest of the parameters [like this](learn_to_code/java/akka/akka_installation_and_config_files?id=chaining-configurations).  

## Reading List Variables From Conf

There are lists in configuration files (like Akka seeding lists) - its possible to read these in with `.getStringList()`. Using my [example](learn_to_code/java/akka/akka_installation_and_config_files?id=example-config-file):  
```
Config myConfig = ConfigFactory.parseResources("yonkered.conf");
List<String> seedNodeList = myConfig.getStringList("akka.cluster.seed-nodes");
System.out.println(seedNodeList.toString());
```

> There are a bunch of other list types as well - for example, you can get `int` with `getIntList()`. Check them out!
