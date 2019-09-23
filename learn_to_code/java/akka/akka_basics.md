# Akka Terms and Overview

# Akka References

Here are some good Akka references I used:
* [lightbend.com](https://developer.lightbend.com/guides/akka-quickstart-java/index.html)
* Unit Testing
 * [UNIT TESTING 1](https://developer.lightbend.com/guides/akka-quickstart-java/testing-actors.html) 
 * [UNIT TESTING 2](https://doc.akka.io/docs/akka/current/testing.html?language=java)

# What is Akka?

Akka is an implementation of the [Actor Model](learn_to_code/java/akka/akka_basics?id=actor-model), which is itself a concepts that competes with threading. Akka's main advantage is that it eliminates the conceopf of shared state - so there is no need to worry about locking shared variables as you would have to using threads. 

In addition, Akka utilizes fault-tolerant distribution - it does this with its use of [supervision](learn_to_code/java/akka/akka_basics?id=supervision) in as well as through its support of running in a distributed setting (so multiple networked machine can work together to accomplish tasks); in short, Akka scales well and can maintain high availability via [supervision](learn_to_code/java/akka/akka_basics?id=supervision).

# Basic Concepts 

## Actor Model

The <font color="green">Actor Model</font> is a theoretical model of concurrent computation. 

## Actor

> Read [here](learn_to_code/java/akka/akka_basics?id=designing-actors) for a practical discussion on designing actors and [here](learn_to_code/java/akka/akka_basics?id=instantiating-actors) on how to instantiate one.

An <font color="green">Actor</font> is a working entity, such as a process or thread. Usually, an actor has one specific role (much like a worker at a company).

An actor _cannot_ be directly interacted with; they can only interact with the outside world via [messages](learn_to_code/java/akka/akka_basics?id=message).
Actors can hold state.

## Location Transparency

Akka supports , ; the location is basically transparent to the developer, meaning the code is very similar to a local mailing address.

Akka has a concept called <font color="green">location transparency</font>, which basically means an which means an [actor's](learn_to_code/java/akka/akka_basics?id=actor) [mailing address](learn_to_code/java/akka/akka_basics?id=mailing-address) is located on a remote location and can run anywhere - locally or on a remote machine. This is possible due to how Actors are created: a factory must be used to create an Actor, as it's impossible to create an instance of an Actor using the `new` keyword.
 * This factory returns a reference that points to an actor instance - _not_ an actor instance itself.
 * This makes a distributed system possible.

As a distributed system, Actors can completely error out and be re-started, which is a powerful advantage of the actor system.
	 

## Message

A <font color="green">message</font> is a piece of data used to communicate with [Actors](learn_to_code/java/akka/akka_basics?id=actor). These can be anything from simple strings to objects.

<font color="green">Messages</font> can be thought of as an entity passing parameters to a method and then obtaining a result (returned object), but <font color="green">messages</font> differ from function / method calls in that <font color="green">messages</font> are asynchronous (i.e. an [actor](learn_to_code/java/akka/akka_basics?id=actor) will process the <font color="green">message</font> when it is ready to do so and is not helf to the whims of other actors or entities). By default, an actor processes <font color="green">messages</font> one at a time. A [mailbox](learn_to_code/java/akka/akka_basics?id=mailbox) is used to queue <font color="green">messages</font> for the actor.

When an [actor](learn_to_code/java/akka/akka_basics?id=actor) receives a <font color="green">message</font> it can:
* change its internal state (i.e. variables)
* Make more actors.
* pass on messages to other actors.


## Message-passing

<font color="green">Message-passing</font> is a concept that explains how messages are passed to invoke behavior instead of directly making a function call.

## Mailing Address

A <font color="green">Mailing Address</font> is where messages are sent for an actor to process when the actor is avaiable.

## Mailbox

A <font color="green">Mailbox</font> is a queue of messages waiting for the actor to process them.


## Actor System

> Read [here](learn_to_code/java/akka/akka_basics?id=creating-the-world-the-actorsystem) for a practical application of an actor system.

An <font color="green">Actor system</font> is a collection of actors and all of their associated information.

## Supervision

<font color="green">Supervision</font> is the concept that a parent actor is responsible for any and all failures of its children. <font color="purple">Fault-tolerance</font> is gained in the actor model with <font color="green">supervision</font>, as <font color="green">supervision</font> removes the burden of handling failure outside of the entity that can fail. A parent actor monitors its children for failures, the parent can re-deploy / re-create the child actor in an attempt to rectify the problem. This assumes that a general 'bad state' was introduced (say, a hiccup in a database connection) that _can_ be repaired with simply re-launching the child.

In addition to <font color="green">supervision</font>, Akka supports running in a distributed manner - so multiple machines across the network can work together to accomplish a goal.


# Installation

> The master Akka Maven repository can be found [here](https://mvnrepository.com/artifact/com.typesafe.akka).

Most of the installs revolve around <font color="purple">Typesafe Activator</font>, which is a bundle that contains a bunch of things such as Scala, Akka, Play, and Simple Build Tool (SBT); that said, I prefer the [Maven](learn_to_code/java/akka/akka_basics?id=maven-no-install-necessary) approach as nothing else is really needed (outside of Maven and the configuration of its POM file, that is).

## Maven (No Install Necessary)

!> I use [Maven](learn_to_code/java/maven) for all of my Java projects (including my Akka examples). In order for you to run any examples I have, you will have to understand a bit about [Maven](learn_to_code/java/maven), how to [compile a Maven project](learn_to_code/java/maven?id=compiling-your-maven-project), and [its directory structure](learn_to_code/java/maven?id=locations-of-files-in-maven). If you do not know what Maven is, [here is a brief overview](learn_to_code/java/maven?id=what-is-the-main-idea-behind-maven).

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


# Creating the World: The ActorSystem

## Basic ActorSystem Creation

> An example of creating an actor system can be found [here](learn_to_code/java/akka/akka_basic_examples?id=akkaquickstartjava).

One of the first things that must be done is the <font color="purple">Akka World / System</font> must be created (its official title is an [Actor System](learn_to_code/java/akka/akka_basics?id=actor-system)); an example of how this is done is:
```
final ActorSystem system = ActorSystem.create("helloakka");
```

Akka needs an <font color="purple">Akka System</font> in which to operate; otherwise, there is a collection of unaffiliated actors running around rampant. The above creates a 'world' and names it "helloakka"; this name is important, as its possible (but not always typical) to have multiple 'worlds' at once, and the name can help identify the 'world' you want to utilize later in the code (to reference the `system` name from above we could use `system.name()`, if we needed to).

In addition, the `ActorSystem` object is usually marked as [final](learn_to_code/java/java_basics?id=final-variables); its best practice to mark `ActorSystem`, `ActorRef`s, and a few other Akka objects as `final`.

## Tearing the World Down

Once your code is about to exit, you must perform some cleanup - you can do this with the `terminate()` function; for example, if you used the creation logic in the [basic ActorSystem creation](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation), this would be:
```
system.terminate();
```

## A Note on Running Akka Code

When you run the code, since threads are used in the background its quite possible your program ends before the threads are finished (or you see the results). Usually there is some sort of a loop deployed, but an easy thing to do would be to utilize system input (waiting for the user to press 'Enter') like this:
```
        final ActorSystem system = ActorSystem.create("helloakka");

        try {
            /*			
			Your super Akka code here
			*/

            //This is needed so the system does not immediately terminate; the app can end before the actors get a chance to finish,
            System.out.println(">>> Press ENTER to exit <<<");
            System.in.read();
        } catch (IOException ioe) {
        } finally {
            //this breaks down the actor system - THIS IS CRITICAL
            system.terminate();
        }
```

## Instantiating Actors

!> It should be noted that _instantiating an actor_ and _designing an actor_ are two separate concepts; the following is about instantiating an actor (more on design can be found [here](learn_to_code/java/akka/akka_basics?id=designing-actors)).

> An example of instantiating an actor can be found [here](learn_to_code/java/akka/akka_basic_examples?id=akkaquickstartjava).

When creating [Actors](learn_to_code/java/akka/akka_basics?id=actor) for a _specific_ [Actor System](learn_to_code/java/akka/akka_basics?id=creating-the-world-the-actorsystem) we must use a factory; they are never directly created (with `new`, for example). 

Two examples of instantiating actors can be taken from [AkkaQuickstart.java](learn_to_code/java/akka/akka_basic_examples?id=akkaquickstartjava) from [this example](learn_to_code/java/akka/akka_basic_examples?id=basic-example-hello-world):
```
final ActorRef printerActor = system.actorOf(Printer.props(), "printerActor");
final ActorRef howdyGreeter = system.actorOf(Greeter.props("Howdy", printerActor), "howdyGreeter");
```
* The <font color="purple">system</font> variable was created like it was in [basic ActorSystem creation](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation).
* The `actorOf()` factory method creates actors and it takes two parameters: 
 * A `props()` configuration object (props() is associated with the Actor's constructor, [see here](learn_to_code/java/akka/akka_basics?id=props-constructor) for more information on `props()`).
 * A name; <font color="red">names are important in Akka and should be clear in what they are</font>.
* The <font color="purple">printerActor</font> is created and then used as a _reference_ variable in <font color="purple">Greeter.props()</font>; this is done to show that its possible to use an Actor reference in other Actors, multiple times if necessary.

Now, we have created two different actors that we can send `tell()`s to, meaning [we can send them messages](learn_to_code/java/akka/akka_basics?id=sending-the-message). 

It should be noted that we do not _have_ to set 'system.actorOf()' to anything - we could have just said `system.actorOf(Printer.props(), "printerActor")` if we simply want to launch this actor 'in the wild', and it would be possible to retrieve it with the name 'printerActor' since we did not use an object to instantiate it. This is usually not done this way, but you must know its possible to do so.

## Sending The Message

> An example of using tell() can be found [here](learn_to_code/java/akka/akka_basic_examples?id=akkaquickstartjava).

If you have [designed an Actor](learn_to_code/java/akka/akka_basics?id=designing-actors), [created an ActorSystem](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation) and then [instantiated some Actors](learn_to_code/java/akka/akka_basics?id=instantiating-actors), we can now send messages to those actors. 

We send messages to Actors with the `tell()` method; is is an example from [AkkaQuickstart.java](learn_to_code/java/akka/akka_basic_examples?id=akkaquickstartjava) from [this example](learn_to_code/java/akka/akka_basic_examples?id=basic-example-hello-world):
```
howdyGreeter.tell(new WhoToGreet("Akka"), ActorRef.noSender());
howdyGreeter.tell(new Greet(), ActorRef.noSender());
```
* This uses the Actors created when we [instantiated some Actors](learn_to_code/java/akka/akka_basics?id=instantiating-actors).
* Recall that the message acts as _both_ a trigger _and_ it carries data.
 * When [designed an Actor](learn_to_code/java/akka/akka_basics?id=designing-actors), the [createReceive() method was overridden](learn_to_code/java/akka/akka_basics?id=overriding-createreceive), and `match()`es were created that accepted a 'trigger' in the form of XXX.class; that class is determined by the _type_ of class sent in the `tell()`.
 * The idea is the `match()` as described when the [createReceive() method was overridden](learn_to_code/java/akka/akka_basics?id=overriding-createreceive) accepts a 'trigger', and this 'trigger' is obtained from a `tell()` elsewhere in the code.
* These two `tell()`s send one message each.
 * The first creates a <font color="purple">WhoToGreet</font> object, which will be picked up bt the `match()` in the [createReceive() method](learn_to_code/java/akka/akka_basics?id=overriding-createreceive).
 * The second creates a <font color="purple">Greet</font> object, which will be picked up bt the `match()` in the [createReceive() method](learn_to_code/java/akka/akka_basics?id=overriding-createreceive).

Note the use of `ActorRef.noSender()`; this is the second parameter sent in `tell()`, and it lets the Actor know _which entity_ sent the `tell()`; in this case, we are choosing to remain anonymous. If we are in an Actor that extends AbstractActor, we could say `getSelf()` here, and then we could simply do work and pass a `tell()` back to the calling entity with `getSender().tell()` (we wouldn't even have to specify _who_ precisely that is in the code).

# Messages (Practical)

Messages act as a 'trigger' for Actors _in addition to_ passing data to Actors.

## Message Class Location

The question of where the message-as-class should be stored; its common to see the message classes be stored in the Actor class itself being called, much like in [Greeter.java](learn_to_code/java/akka/akka_basic_examples?id=greeterjava) in [this example](learn_to_code/java/akka/akka_basic_examples?id=basic-example-hello-world). This is usually considered best practice, but that said, the message-as-class can reside anywhere.

## Messages Not Thread-Safe

You may notice the usage of 'final' in when it comes to actually constructing messages that will be sent to Actors; this is because messages should be immutable as they are shared between different threads - ** messages are not thread-safe**. The idea is to lock in the values _first_, _then_ send the unchangeable object through the actor threads.

The problem is messages are passed by _reference_ and not by value through the various Actors (at least locally); this means if the message itself is not immutable - or, if a message is sent to an Actor, is altered, and then quickly sent to a second actor, the altered changed may be used by the initial Actor.

It is possible to create an object and then send it as a message _and_ data as I did [in this example](learn_to_code/java/akka/akka_basic_examples?id=basic-example-hello-world):
```
final RaisingArizonaGreeter mcdunnough = new RaisingArizonaGreeter("panty");
howdyGreeter.tell(mcdunnough, ActorRef.noSender());
```

However, if Actor 'A' was passed the <font color="purple">mcdunnough</font> object, then the <font color="purple">item</font> variable in <font color="purple">mcdunnough</font> was quickly changed, and then the <font color="purple">mcdunnough</font> object was sent to Actor 'B', its quite possible the altered <font color="purple">mcdunnough</font> object is used by _both_ 'A' and 'B' instead of just 'B'. Usually, messages are constructed on the fly and not like the <font color="purple">mcdunnough</font> object (for the given reason), but I wanted to showcase that this is possible.

It is because of this that it is suggested to sbuild and send the message 'on the fly' - that is to say, send _all necessary variables_ to the class constructor and send an immediate instance of the object like so:
```
howdyGreeter.tell(new RaisingArizonaGreeter("panty"), ActorRef.noSender());
```


# Designing Actors

When designing a class that will become an actor in Akka, it must extend an Akka actor class; the most prevalent one would be the `AbstractActor`. An example of an Akka actor class definition would be:
```
public class Greeter extends AbstractActor {
	...
	...
	...
}
```
* This creates a class called 'Greeter'.

## Props() Constructor

> An example of the `Props()` constructor can be found [here](learn_to_code/java/akka/akka_basic_examples?id=akkaquickstartjava).

The `Props` method is used to describe how to construct the Actor; it is considered best practice to use one. <font color="purple">XXX</font>.props is a _special_ constructor specifically for actors; it _defines_ **which type** of actor is being instantiated in the actor system as well acts as a traditional constructor for a passed class once that actor is instantiated. 

It is required to pass any argument that is passed to the constructor to be passed to `props`, as `props` simply takes the arguments that the class itself takes (or would normally take in the constructor). The constructor _should still be defined separately if it has any parameters_ and you should write said constructor as normal (although it will never directly be used, outside of the call to XXX.props()).


An example of how one is constructed is:
```
    static public Props props(String message) {
        return Props.create(Greeter.class, () -> new Greeter(message));
    }
```
* This example is for a class called <font color="purple">Greeter</font>.
* Under most circumstances, the body of the method simply has one line, which returns a `Props.create()` with two parts:
 * The name of the class with the `.class` extension.
 * A [lambda function](learn_to_code/java/java_basics?id=lambda-function) which basically acts as a stand-in for utilizing the constructor of the given class.


## Overriding createReceive()

> An example of overriding the `createReceive()` can be found [here](learn_to_code/java/akka/akka_basic_examples?id=akkaquickstartjava).

If the Actor class extends AbstractActor (among others) it _must_ override the `createReceive()` method. This should return a `Receive` object. A general flow of this is:
* create `ReceiveBuilder` object.
* Add a bunch of `match()` criteria.
 * There can be multiple `match()` criteria.
* Top it off with a call to `build()`.

All parts are critical, but its important to understand what `match()` does here - `match()` is the _specific_ bit of code that is _listening_ for other objects in the system to send this partcular Actor a message. Each `match()` listens for a _specific message type_, and once the specific message type is received, it launches code that handles the given object.

An example of this is the paired down version in [Greeter.java](learn_to_code/java/akka/akka_basic_examples?id=greeterjava) in [this example](learn_to_code/java/akka/akka_basic_examples?id=basic-example-hello-world):
```
    @Override
    public Receive createReceive() {
        return receiveBuilder()
                .match(WhoToGreet.class, wtg -> {
                    this.greeting = message + ", " + wtg.who;
                })
                .build();
    }
```
* Note I removed slightly more compliated `match()` es in this, so its a bit different from the original.
* `receiveBuilder()` simply runs `ReceiveBuilder.create()`.
* Next are the `match()`es
 * There can be multiple matches
 * `match()` accepts two parameters
   * The name of the class with the `.class` extension - **this is the message type this particular** `match()` **is listening for**.
   * A [lambda function](learn_to_code/java/java_basics?id=lambda-function) which basically acts as a stand-in for utilizing the constructor of the given class.
* More on the lambda function
 * Keep in mind that the sent class serves a dual purpose - it acts as a trigger to our Actor to do something based off the _specific_ class, but it _also_ contains an object of that type that we can interact with (at the very least, read variables from).
 * The lambda function defines _what_ we would like to do once we receive the message.
 * The first part (before the ->) basically turns the sent object (in this case, a <font color="purple">WhoToGreet</font> object) into a variable we can use; now, the variable <font color="purple">wtg</font> represents a passed <font color="purple">WhoToGreet</font> object.
 * The actual function itself; this defines _what_ we want to do once we receive this object type - in this case, we set our own <font color="purple">greeting</font> String to a (previously determined) <font color="purple">message</font> _plus_ the 'who' variable that is inside the sent <font color="purple">WhoToGreet</font> object (i.e. <font color="purple">wtg</font>). we can access <font color="purple">wtg.who</font>, which is a public `String` in the <font color="purple">WhoToGreet</font> class.
   * This can be far more complicated if we wish - the only job 
* The final thing done is `build()` which finished building the 'listener'.

> `match()` is reached by a `tell()`
