# Akka Terms and Overview

# Akka References

Here are some good Akka references I used:
* [lightbend.com](https://developer.lightbend.com/guides/akka-quickstart-java/index.html)


# What is Akka?

Akka is an implementation of the [Actor Model](learn_to_code/java/akka/akka_basics?id=actor-model), which is itself a concepts that competes with threading. Akka's main advantage is that it eliminates the conceopf of shared state - so there is no need to worry about locking shared variables as you would have to using threads. 

In addition, Akka utilizes fault-tolerant distribution - it does this with its use of [supervision](learn_to_code/java/akka/akka_basics?id=supervision) in as well as through its support of running in a distributed setting (so multiple networked machine can work together to accomplish tasks); in short, Akka scales well and can maintain high availability via [supervision](learn_to_code/java/akka/akka_basics?id=supervision).


# Actor Model

The <font color="green">Actor Model</font> is a theoretical model of concurrent computation. 

# Actor

An <font color="green">Actor</font> is a working entity, such as a process or thread. Usually, an actor has one specific role (much like a worker at a company).

An actor _cannot_ be directly interacted with; they can only interact with the outside world via [messages](learn_to_code/java/akka/akka_basics?id=message).
Actors can hold state.

# Location Transparency

Akka supports <font color="green">location transparency</font>, which means an [actor's](learn_to_code/java/akka/akka_basics?id=actor) [mailing address](learn_to_code/java/akka/akka_basics?id=mailing-address) is located on a remote location; the location is basically transparent to the developer, meaning the code is very similar to a local mailing address.

# Message

A <font color="green">message</font> is a piece of data used to communicate with [Actors](learn_to_code/java/akka/akka_basics?id=actor). These can be anything from simple strings to objects.

<font color="green">Messages</font> can be thought of as an entity passing parameters to a method and then obtaining a result (returned object), but <font color="green">messages</font> differ from function / method calls in that <font color="green">messages</font> are asynchronous (i.e. an [actor](learn_to_code/java/akka/akka_basics?id=actor) will process the <font color="green">message</font> when it is ready to do so and is not helf to the whims of other actors or entities). By default, an actor processes <font color="green">messages</font> one at a time. A [mailbox](learn_to_code/java/akka/akka_basics?id=mailbox) is used to queue <font color="green">messages</font> for the actor.

When an [actor](learn_to_code/java/akka/akka_basics?id=actor) receives a <font color="green">message</font> it can:
* change its internal state (i.e. variables)
* Make more actors.
* pass on messages to other actors.


# Message-passing

<font color="green">Message-passing</font> is a concept that explains how messages are passed to invoke behavior instead of directly making a function call.

# Mailing Address

A <font color="green">Mailing Address</font> is where messages are sent for an actor to process when the actor is avaiable.

# Mailbox

A <font color="green">Mailbox</font> is a queue of messages waiting for the actor to process them.


# Actor System

An <font color="green">Actor system</font> is a collection of actors and all of their associated information.

# Supervision

<font color="green">Supervision</font> is the concept that a parent actor is responsible for any and all failures of its children. <font color="purple">Fault-tolerance</font> is gained in the actor model with <font color="green">supervision</font>, as <font color="green">supervision</font> removes the burden of handling failure outside of the entity that can fail. A parent actor monitors its children for failures, the parent can re-deploy / re-create the child actor in an attempt to rectify the problem. This assumes that a general 'bad state' was introduced (say, a hiccup in a database connection) that _can_ be repaired with simply re-launching the child.

In addition to <font color="green">supervision</font>, Akka supports running in a distributed manner - so multiple machines across the network can work together to accomplish a goal.

# Installation

> The master Akka Maven repository can be found [here](https://mvnrepository.com/artifact/com.typesafe.akka).

Most of the installs revolve around <font color="purple">Typesafe Activator</font>, which is a bundle that contains a bunch of things such as Scala, Akka, Play, and Simple Build Tool (SBT); that said, I prefer the [Maven](learn_to_code/java/akka/akka_basics?id=maven-no-install-necessary) approach as nothing else is really needed (outside of Maven and the configuration of its POM file, that is).

## Maven (No Install Necessary)

The nice thing about [Maven](learn_to_code/java/maven) is it can bundle all necessary jar files for your project - including Akka - where the only installs required are [Maven](learn_to_code/java/maven?id=ubuntu-install) and [Java](ubuntu/server_build?id=installing-java-from-oracle) itself.

To install Akka to [Maven](learn_to_code/java/maven), use [these dependency entries in Maven](learn_to_code/java/maven?id=adding-dependencies-to-maven):

```
        <dependency>
          <groupId>com.typesafe.akka</groupId>
          <artifactId>akka-actor_2.11</artifactId>
          <version>2.5.25</version>
        </dependency>

        <dependency>
            <groupId>com.typesafe.akka</groupId>
            <artifactId>akka-testkit_2.11</artifactId>
            <version>2.5.25</version>
            <scope>test</scope>
        </dependency>

        <dependency>
          <groupId>com.typesafe.akka</groupId>
          <artifactId>akka-stream_2.11</artifactId>
          <version>2.5.25</version>
        </dependency>
		
        <dependency>
          <groupId>com.typesafe.akka</groupId>
          <artifactId>akka-slf4j_2.11</artifactId>
          <version>2.5.25</version>
        </dependency>		
			
        <dependency>
          <groupId>com.typesafe.akka</groupId>
          <artifactId>akka-agent_2.11</artifactId>
          <version>2.5.25</version>
        </dependency>

```

!> If using IntelliJ, IntelliJ can have some trouble recognizing imported projects. If you import the project then run a `mvn clean install`, the libraries exist but may not be recognized by IntelliJ. I had to do a few things and I am not sure which one worked; I tried right-clicking the project, then `Maven -> Reimport`; I then tried right-clicking the project, then `Maven -> Download Sources`; finally, I tried `Run -> Edit Configurations` then `Templates -> Maven`, went to the 'General' tab, unclicked 'Use project settings', clicked 'Override' next to the 'Local repository', clicked 'Apply', then went back in and reversed those changes. I do not know which one worked, but one of these got IntelliJ to recognize `import akka.actor.Actor;`. Try `Maven -> Reimport` first.

## Windows Typesafe Activator Install

Download Typesafe Activator from [here](http://www.typesafe.com/get-started).

## Ubuntu Typesafe Activator Install

1\. Download Typesafe Activator from [here](http://www.typesafe.com/activator/download), and if that does not work, try [here](http://www.typesafe.com/get-started).

2\. [Become root](/ubuntu/linux_notes?id=becoming-root).

3\. Determine where you would like to open the zip file - a good choice would be `/opt/activator`. If this does not exist, create it, then move the `.zip` file to this folder and use the [unzip command](ubuntu/linux_notes?id=unzip-extracting-files) to unzip it.

> `unzip` keeps the structure of its folders, so it will create any folders inside of it. I am not sure if the Typesafe Activator `.zip` file has an immediate subdirectory (with nothing else on the first level); if this is the case, you can probably just store the `.zip` file in `/opt` and then [unzip](ubuntu/linux_notes?id=unzip-extracting-files) it from there; these instructions will assume the base path is `opt/activator` but if it is not, you will have to slightly modify the instructions from here on out for the install.

4\. Make the Activator executable:
```
chmod 755 /opt/activator/activator
```

5\. Add the Activator to (everyone's) path by editing the bottom of `/etc/profile` (we are using [VI](ubuntu/linux_notes?id=using-vi), but if you are not comfortable with VI use another text editor):
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

10\. 

Make a directory [Update all packages](/ubuntu/linux_notes?id=updating-upgrading-all-packages).

