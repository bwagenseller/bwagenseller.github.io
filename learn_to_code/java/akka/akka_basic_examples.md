# Akka Examples

# Basic Example: Hello World!

> The basics of this comes from [lightbend](https://developer.lightbend.com/guides/akka-quickstart-java/define-actors.html).

This file uses [this particular pom.xml file for Maven](learn_to_code/java/akka/akka_basic_examples?id=pomxml) and 4 other files (all 4 .java files are in the same directory):
* AkkaQuickStart.java - This houses the `main()` function and will create the actor system and the world.
* Printer.java - This will be the printer actor,
* Greeter.java - This will be the greeter actor.
* RaisingArizonaGreeter.java - An additional greeter that will showcase using an existing object as both a message and data (and showcases one of my favorite movies, _Raising Arizona_).

## AkkaQuickstart.java

```
package com.wagenseller;

import java.io.IOException;

import com.wagenseller.Greeter.Greet;
import com.wagenseller.Greeter.WhoToGreet;

import akka.actor.ActorRef;
import akka.actor.ActorSystem;

public class AkkaQuickstart {
    //FROM: https://developer.lightbend.com/guides/akka-quickstart-java/define-actors.html

    //The ActorSystem manages the life cycles of all of the actors it contains

    private static void runCode(String[] args) {
        //create the container in which Akka will run

        final ActorSystem system = ActorSystem.create("helloakka");

        //this is a way to access the actor system's name
        System.out.println("This actor system's name is '" + system.name() + "'.");

        try {
            //create a printer actor
            //
            //The actorOf() factory method takes two parameters: A) a 'props()' configuration object (props() is associated with the Actor's constructor, see the description in
            // Greeter.java) and B) a name.
            // * Names are important in Akka and should be clear in what they are
            //We do not have to set 'system.actorOf()' to anything - so we could have just said `system.actorOf(Printer.props(), "printerActor")` if we simply want to launch this
            //actor 'in the wild', and it would be possible to retrieve it with the name 'printerActor' since we did not use an object to instantiate it. This is usually not done this
            //way but its possible.
            //
            //This example uses the reference for printerActor in every greeter, but in reality each one could have had a different printerActor (although this is done to show that its
            // possible to use the Actor reference multiple times)
            final ActorRef printerActor = system.actorOf(Printer.props(), "printerActor");

            //create three greeter actors
            final ActorRef howdyGreeter = system.actorOf(Greeter.props("Howdy", printerActor), "howdyGreeter");
            final ActorRef helloGreeter = system.actorOf(Greeter.props("Hello", printerActor), "helloGreeter");
            final ActorRef goodDayGreeter = system.actorOf(Greeter.props("Good day", printerActor), "goodDayGreeter");


            //send the message to the greeter actor instance - but do NOT send it to the printer actor yet
            //It seems the way to send a message to an actor is via the .tell() method, which seems to have two parameters: 1. The triggering message and 2. the object itself to be worked on
            //This simply puts the message into the Actor's mailbox - when the Actor is free it will process it
            howdyGreeter.tell(new WhoToGreet("Akka"), ActorRef.noSender());

            //send the message to the printer
            howdyGreeter.tell(new Greet(), ActorRef.noSender());

            //send the message to the greeter - then the actor - of the other messages
            howdyGreeter.tell(new WhoToGreet("Lightbend"), ActorRef.noSender());
            howdyGreeter.tell(new Greet(), ActorRef.noSender());

            helloGreeter.tell(new WhoToGreet("Java"), ActorRef.noSender());
            helloGreeter.tell(new Greet(), ActorRef.noSender());

            goodDayGreeter.tell(new WhoToGreet("Play"), ActorRef.noSender());
            goodDayGreeter.tell(new Greet(), ActorRef.noSender());

            /*
            The other examples showed how to send a tell with a random class built on the fly, but this example shows a class that is established before the tell() - and
            completely independent from the actor system - and it is still used as a message.
            
			This should also drive home the point that a class acts as both _a message **and** data to be processed_; in this case, the 'fingerprint' of the class itself
            ('RaisingArizonaGreeter') is used by the 'Greeter' actor class, and the associated object (which stores the word 'panty') - and its associated data - can be
            fully used.
			

            Finally, it should be noted that using the below approach can lead to problems, as _messages are passed by reference and are thus not thread-safe_. If Actor 'A' was passed
            the 'mcdunnough' object below, the item was quickly changed in the 'mcdunnough' object, and then the 'mcdunnough' object was sent to Actor 'B', its quite possible the altered
            'mcdunnough' object is used by _both_ 'A' and 'B' instead of just 'B'. Usually, messages are constructed on the fly (as above) and not like the 'mcdunnough" object below (for the
            given reason), but I wanted to showcase that this is possible.			
             */
            RaisingArizonaGreeter mcdunnough = new RaisingArizonaGreeter("panty");
            howdyGreeter.tell(mcdunnough, ActorRef.noSender());


			//This is needed so the system does not immediately terminate; the app can end before the actors get a chance to finish,
            System.out.println(">>> Press ENTER to exit <<<");
            System.in.read();
        } catch (IOException ioe) {
        } finally {
            //this breaks down the actor system - THIS IS CRITICAL
            system.terminate();
        }

    }

    public static void main(String[] args) {
        runCode(args);
    }
}
```

## Greeter.java

```
package com.wagenseller;

import akka.actor.AbstractActor;
import akka.actor.ActorRef;
import akka.actor.Props;
import com.wagenseller.Printer.Greeting;

//Extend the AbstractActor, which officially defines this class as an actor - with all the rights and responsibilities as such - and als requires an override of 'createReceive()'
public class Greeter extends AbstractActor {
    /*
    Props is used to describe how to construct the Actor - this is considered best practice
    Props seems to be required as
    The specific Greeter class accepts two parameters: a message and a printerActor

    XXX.props (in this case, Greeter.props()) is a _special_ constructor specifically for actors; it _defines_ **which type** of actor is being instantiated in the actor system
    as well acts as a traditional constructor for a passed class.

    As for arguments, it simply takes the arguments that the class itself takes (or would normally take in the constructor)
    The constructor _should still be defined separately if it has any parameters_ and you should write said constructor as normal (although it will never directly be used, outside
    of the call to XXX.props()).

     */
    static public Props props(String message, ActorRef printerActor) {
        return Props.create(Greeter.class, () -> new Greeter(message, printerActor));
    }

    //Messages can be a class - WhoToGreet stores a String 'who'.
    //In general, the classes that act as messages should be stored in the Actor itself to avoid confusion
    static public class WhoToGreet {
        public final String who;

        public WhoToGreet(String who) {
            this.who = who;
        }
    }

    //This class is purely used to instruct the actor to execute the greeting.
    //The class is defined here, but its empty - all that its used for is to trigger Greeter's createReceive
    static public class Greet {
        public Greet() {
        }
    }


    private final String message;
    private final ActorRef printerActor;
    private String greeting = "";

    public Greeter(String message, ActorRef printerActor) {
        this.message = message;
        this.printerActor = printerActor;
    }

    //it is critical to overwrite the class 'createReceive()'
    //It seems each individual .match() function accepts a trigger message (in the first case below, the trigger is receiving a 'WhoToGreet' class and the second .match() function
    //is triggered by receiving a 'Greet' Class (which the Greet class has only a skeleton code behind - its only point of existence is to trigger printerActor
    @Override
    public Receive createReceive() {
        return receiveBuilder()
                .match(WhoToGreet.class, wtg -> {
                    this.greeting = message + ", " + wtg.who;
                })
                .match(Greet.class, x -> {
                    printerActor.tell(new Greeting(greeting), getSelf());
                })
                .match(RaisingArizonaGreeter.class, baby -> {
                    /*
                    This shows that a class can be both _a message **and** data to be processed_; in this case, a class is simply passed to the 'tell()' function, and
                    the 'fingerprint' of the class itself ('RaisingArizonaGreeter') is extrapolated as well as the object (in this case its identified as variable 'baby').
                    'baby' can now act as the 'RaisingArizonaGreeter' object that was passed, which means '.sayIt()' is available (in addition, 'RaisingArizonaGreeter.item' is also
                    available, which was set _before_ the 'tell()' to the 'Greeter' class was sent).

                    We extract the string from 'baby.sayIt()', send that to the 'Greeting' constructor, and then send all of this to  the printerActor.

                    It should also be noted that we send 'getSelf()' as well, which notifies the printerActor where this message came from (it actually has the ability to send
                    a message back to this actor with the method 'getSender()', if you wish)
                    */
                    printerActor.tell(new Greeting(baby.sayIt()), getSelf());
                })
                .build();
    }
}
```

## Printer.java

```
package com.wagenseller;

import akka.actor.AbstractActor;
import akka.actor.Props;
import akka.event.Logging;
import akka.event.LoggingAdapter;

public class Printer extends AbstractActor {
    static public Props props() {
        return Props.create(Printer.class, () -> new Printer());
    }

    static public class Greeting {
        public final String message;

        public Greeting(String message) {
            this.message = message;
        }
    }

    //this creates a logger - not quite sure how this works yet
    private LoggingAdapter log = Logging.getLogger(getContext().getSystem(), this);

    public Printer() {
    }

    //simply log to the logger
    //the only match() function triggers off a Greeting class and takes a Greeter object as a parameter
    @Override
    public Receive createReceive() {
        return receiveBuilder()
                .match(Greeting.class, greeting -> {
                    //Simply print the message to the logger
                    log.info(greeting.message);
                })

                .build();
    }
}
```

## RaisingArizonaGreeter.java

```
package com.wagenseller;

public class RaisingArizonaGreeter {

    private String item;

    public RaisingArizonaGreeter (String item) {
        this.item = item;
    }
	
    public void setItem(String item) {
        this.item = item;
    }	

    public String sayIt() {
        return "Son, you've got a " + item + " on your head.";
    }
}
```

## 'Hello World' Explained

The basics: we must create an actor system, which includes Actors that can send a `tell()` to other Actors, who, in turn, have a `match()` method that 'listens' for specific 'triggers' included in sent `tell()`s. If a trigger is found, the actor specifies what specifically should be done within the `match()` [lambda function](learn_to_code/java/java_basics?id=lambda-function).

**<font size="4">Creating The Akka Actor System (<font color="purple">AkkaQuickstart.java</font>)</font>**

One of the first things that must be done is we must create the [Akka System](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation); this is done in the line:
```
final ActorSystem system = ActorSystem.create("helloakka");
```

Now some [actors must be instantiated](learn_to_code/java/akka/akka_basics?id=instantiating-actors), and that is done in the lines
```
final ActorRef printerActor = system.actorOf(Printer.props(), "printerActor");
final ActorRef howdyGreeter = system.actorOf(Greeter.props("Howdy", printerActor), "howdyGreeter");
final ActorRef helloGreeter = system.actorOf(Greeter.props("Hello", printerActor), "helloGreeter");
final ActorRef goodDayGreeter = system.actorOf(Greeter.props("Good day", printerActor), "goodDayGreeter");
```

The first actor created is a Printer actor, and the last three are Greeter actors. The [props()](learn_to_code/java/akka/akka_basics?id=props-constructor) acts as a stand-in constructor that is specific to Akka - it simultaneously tells Akka _which_ actor is being instantiated as well as sets up its constructor; also note a name is assigned to each Actor (in addition to its variable identifier).

Next, we [send tell() calls](learn_to_code/java/akka/akka_basics?id=sending-the-message) to send messages to the Actors; each `tell()` contains a [message](learn_to_code/java/akka/akka_basics?id=messages-practical), which is usually a specified class (or, an object and its class is determined from the object). The `tell()` is eventually picked up by a `match()` in the [createReceive()](learn_to_code/java/akka/akka_basics?id=overriding-createreceive) method in the Actor itself, which uses the sent class as a 'trigger' and its associated object as its data (the sent object acts as _both_ the trigger _and_ the specific data required).

We then wait for the user to press `Enter`, at which point the Akka system is destroyed with `system.terminate()`.

**<font size="4">Designing The Actors</font>**

The first thing of note is the each Actor must extend some Akka Actor class - the example above extends `AbstractActor`, which is a common one to extend. The [props()](learn_to_code/java/akka/akka_basics?id=props-constructor) constructor is defined, and then the `match()`es are built out in [the createReceive()](learn_to_code/java/akka/akka_basics?id=overriding-createreceive) method. Here is a shortened version of `match()` from Greeter:
```
.match(WhoToGreet.class, wtg -> {
	this.greeting = message + ", " + wtg.who;
})
.match(Greet.class, x -> {
	printerActor.tell(new Greeting(greeting), getSelf());
})
.match(RaisingArizonaGreeter.class, baby -> {
	printerActor.tell(new Greeting(baby.sayIt()), getSelf());
})
```

The first `match()` looks for an incoming <font color="purple">WhoToGreet</font> object; if one is sent via a `tell()`, the <font color="purple">wtg</font> variable represents the incoming object and the greeting is set with the name (<font color="purple">wtg.who</font>). The second `match()` looks for a <font color="purple">Greet</font> class, and if one is found a `tell()` is sent to the Printer actor. The final `match()` looks for a <font color="purple">RaisingArizonaGreeter</font> object, and if one is sent it names the object <font color="purple">baby</font> and sends a <font color="purple">Greeting</font> `tell()` to the printer actor.


# Index

> This is just an index of things for this page - a few things may reference these items so I put them in a centralized place.

## pom.xml

> In order for this to work, you will have to understand a bit about [Maven](learn_to_code/java/maven), how to [compile a Maven project](learn_to_code/java/maven?id=compiling-your-maven-project), and [its directory structure](learn_to_code/java/maven?id=locations-of-files-in-maven). If you do not know what Maven is, [here is a brief overview](learn_to_code/java/maven?id=what-is-the-main-idea-behind-maven).

I use [Maven](learn_to_code/java/maven) for all of my Java projects, so all of them (including my Akka examples) need a `pom.xml` file - here is a general one I use for Akka. Use this (with the noted modifications) unless otherwise stated. 

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.wagenseller</groupId>
  <artifactId>akka-test</artifactId>
  <packaging>jar</packaging>
  <version>1.0.0-SNAPSHOT</version>
  <name>akka-test</name>
  <url>http://maven.apache.org</url>
  <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
          <configuration>
              <source>1.11</source>
              <target>1.11</target>
              <release>11</release>
          </configuration>
        </plugin>
      </plugins>
  </build>
  <dependencies>
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
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
* You may wish to change the `<artifact>` and `<name>` fields from `akka-test` to whatever you like, that is your choice.
* You may wish to change the `<groupId>` field from `com.wagenseller` to whatever you like, that is your choice. Note that if you change this, you may have to alter their location in the Maven hierarchy.
* You are free to change the versions of each artifact / pacakge, but you will [need to look them up](learn_to_code/java/maven?id=adding-dependencies-to-maven) if you wish to do this.