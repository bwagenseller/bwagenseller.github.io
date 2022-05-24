# Akka Terms and Overview

# Akka References

Here are some good Akka references I used:
* [doc.akka.io](https://doc.akka.io/docs/akka/current/actors.html#introduction)
 * [untyped actors on doc.akka.io](https://doc.akka.io/docs/akka/2.1/java/untyped-actors.html)
* [lightbend.com](https://developer.lightbend.com/guides/akka-quickstart-java/index.html)
 * [Lightbend Academy](https://academy.lightbend.com/)  
   * [Lightbend's Training Dashboard](https://academy.lightbend.com/dashboard)  
 * [Lightbend Getting Started Guides](https://developer.lightbend.com/guides/)  
 * [When To Use Play, Lagom, Or Akka HTTP For Your Project by Lightbend](https://soundcloud.com/lightbend/when-to-use-play-lagom-or-akka-http) (Discussion)  
* Unit Testing
 * [UNIT TESTING 1](https://developer.lightbend.com/guides/akka-quickstart-java/testing-actors.html) 
 * [UNIT TESTING 2](https://doc.akka.io/docs/akka/current/testing.html?language=java)
* [Artery Remoting](https://doc.akka.io/docs/akka/current/remoting-artery.html#artery-remoting)  
* [Cluster Usage](https://doc.akka.io/docs/akka/current/typed/cluster.html#cluster-usage)  
* [Cluster Singleton](https://doc.akka.io/docs/akka/current/cluster-singleton.html)  
* [Differences between actor pools and groups in Akka](https://stackoverflow.com/questions/29004171/difference-between-actor-pools-and-groups-in-akka)  


# What is Akka?

Akka is an implementation of the [Actor Model](learn_to_code/java/akka/akka_basics?id=actor-model), which is a theoretical model of concurrent computation; boiled down, Akka is similar to multithreading but with capabilities across a distributed system. One of Akka's main advantages is that it eliminates the concept of shared state - so there is no need to worry about locking shared variables as you would have to using threads. 

In addition, Akka utilizes fault-tolerant distribution - it does this with its use of [supervision](learn_to_code/java/akka/akka_basics?id=supervision) in as well as through its support of running in a distributed setting (so multiple networked machine can work together to accomplish tasks); in short, Akka scales well and can maintain high availability via [supervision](learn_to_code/java/akka/akka_basics?id=supervision).  

# Basic Concepts 

## Actor Model

The <font color="green">Actor Model</font> is a theoretical model of concurrent computation. 

## Actor

> Read [here](learn_to_code/java/akka/akka_basics?id=designing-actors) for a practical discussion on designing actors and [here](learn_to_code/java/akka/akka_basics?id=instantiating-actors) on how to instantiate one.

An <font color="green">Actor</font> is a working entity, such as a process or thread. Usually, an actor has one specific role (much like a worker at a company).

An actor _cannot_ be directly interacted with; they can only interact with the outside world via [messages](learn_to_code/java/akka/akka_basics?id=message).  Actors can hold state.

## Location Transparency

Akka supports <font color="green">location transparency</font>; the location is basically transparent to the developer, meaning the code is very similar to a local mailing address.

Akka has a concept called <font color="green">location transparency</font>, which basically means an [actor's](learn_to_code/java/akka/akka_basics?id=actor) [mailing address](learn_to_code/java/akka/akka_basics?id=mailing-address) is located on a remote location and can run anywhere - locally or on a remote machine. This is possible due to how Actors are created: a factory must be used to create an Actor, as it's impossible to create an instance of an Actor using the `new` keyword.
 * This factory returns a reference that points to an actor instance - _not_ an actor instance itself.
 * This makes a distributed system possible.

As a distributed system, Actors can completely error out and be re-started, which is a powerful advantage of the actor system.
	 

## Message

A <font color="green">message</font> is a piece of data used to communicate with [Actors](learn_to_code/java/akka/akka_basics?id=actor). These can be anything from simple strings to Java objects.

You can think of a <font color="green">message</font> as an entity passing parameters to a method and then obtaining a result (returned object), but <font color="green">messages</font> differ from function / method calls in that <font color="green">messages</font> are asynchronous (i.e. an [actor](learn_to_code/java/akka/akka_basics?id=actor) will process the <font color="green">message</font> when it is ready to do so and is not held to the whims of other actors or entities). By default, an actor processes <font color="green">messages</font> one at a time. A [mailbox](learn_to_code/java/akka/akka_basics?id=mailbox) is used to queue <font color="green">messages</font> for the actor.

When an [actor](learn_to_code/java/akka/akka_basics?id=actor) receives a <font color="green">message</font> it can:
* change its internal state (i.e. variables)
* Make more actors.
* pass on messages to other actors.


## Message-passing

The <font color="green">Message-passing</font> concept is one that explains how messages are passed to invoke behavior instead of directly making a function call.

## Mailing Address

A <font color="green">Mailing Address</font> is where messages are sent for an actor to process when the actor is avaiable.

## Mailbox

A <font color="green">Mailbox</font> is a queue of messages waiting for the actor to process them.


## Actor System

> Read [here](learn_to_code/java/akka/akka_basics?id=creating-the-world-the-actorsystem) for a practical application of an actor system.

An <font color="green">Actor system</font> is a collection of actors and all of their associated information.

## Supervision

The concept of <font color="green">Supervision</font> is a parent actor is responsible for any and all failures of its children. <font color="purple">Fault-tolerance</font> is gained in the actor model with <font color="green">supervision</font>, as <font color="green">supervision</font> removes the burden of handling failure outside of the entity that can fail. A parent actor monitors its children for failures, the parent can re-deploy / re-create the child actor in an attempt to rectify the problem. This assumes that a general 'bad state' was introduced (say, a hiccup in a database connection) that _can_ be repaired with simply re-launching the child.

In addition to <font color="green">supervision</font>, Akka supports running in a distributed manner - so multiple machines across the network can work together to accomplish a goal.

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

Messages act as a 'trigger' for Actors _in addition to_ passing data to Actors. A <font color="green">Message</font> is _any_ Java object.

## Message Class Location

The question of where the message-as-class should be stored; its common to see the message classes be stored in the Actor class itself being called, much like in [Greeter.java](learn_to_code/java/akka/akka_basic_examples?id=greeterjava) in [this example](learn_to_code/java/akka/akka_basic_examples?id=basic-example-hello-world). 

The above can be done, but once you get into [remoting](learn_to_code/java/akka/remote_and_clustering) (that is to say, actors on foreign servers) this method falls apart: your message class will need to implement `Serializable`, which is [considered to be bad form](https://manuel.bernhardt.io/2018/07/20/akka-anti-patterns-java-serialization/) - the biggest drawback is it is a security risk. Its far more preferable to use a pre-built binary serialization protocol such as [Google Protocol Buffers](https://developers.google.com/protocol-buffers/) (which is mentioned in the previously [linked article](https://manuel.bernhardt.io/2018/07/20/akka-anti-patterns-java-serialization/)).  The advantage to Google's Protocol Buffers is they are natively used in Akka internally.

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

## Actor Names and Paths  

Each actor has a <font color="purple">path</font> and a <font color="purple">name</font>. Two examples of <font color="purple">names</font> can be found [in this section](learn_to_code/java/akka/akka_basics?id=instantiating-actors) (in that example, the actor names created are `printerActor` and `howdyGreeter`). When creating an actor, you only need to make the new <font color="purple">name</font>; you do not directly name the <font color="purple">path</font> (I will explain that in a bit).

The real form of an actor's <font color="purple">name</font> and <font color="purple">path</font> is as follows: 

`akka://helloakka@127.0.0.1:25520/user/someParentActor/someActor`  

This also has the <font color="purple">IP</font> and <font color="purple">port</font> of the actor system; this is important when you are trying to connect to a foreign actor system when [remoting in or connecting to a cluster](learn_to_code/java/akka/remote_and_clustering); however, if your actor system is local, the <font color="purple">IP</font> and <font color="purple">port</font> are not necessary.  

The basic format of an actor <font color="purple">path</font> and <font color="purple">name</font> are as follows:  

`akka://helloakka/user/someParentActor/someActor`  

The sections are as follows:  
* Starts with `akka://` - this is known as the <font color="green">protocol</font>.  
* Next is the <font color="green">Akka system name</font>, which is created when you [create the ActorSystem](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation).  
* Next is the word `user`. There are other defaults here too: `system`, `deadLetters`, `temp`, and `remote` are the others.  These are called <font color="green">guardians</font>; `user` is for _all_ actors you create. So, when referencing actors you created, this will always be `user`.  
* From here, its a tree of parents and then children separated by a `/`; in the example above, the next item is the parent of the actor, and the parent's name is `someParentActor`.  
 * There will be an arbitrary number of ancestors, from 0 to multiple.  
* The final word is the name of the actor itself.  In the above case, thats `someActor`.  

I mentioned before that you cannot directly name the path; however, you can sort of manipulate it. If you create the actor using the `ActorSystem` object (as was done [here](learn_to_code/java/akka/akka_basics?id=instantiating-actors)), the actor will be located immediately after the `user` keyword.

You have a few options if you are inside of an actor - the first option is to create a child actor using `context()`; this will force the new actor to be a child of the actor in which its launched. You can do this like so:  
```
ActorRef someActorObject = context().actorOf(someActor.props(), "someChildActor");
```  
 * `someActor.props()` is the [props](learn_to_code/java/akka/akka_basics?id=props-constructor) of an actor.  
 
If, instead, you are in an actor and want to make an actor that uses the root of `user/` instead, you could use `context().system()` like so:  
```
ActorRef someActorObject = context().system().actorOf(someActor.props(), "someActor");
```  
 * `someActor.props()` is the [props](learn_to_code/java/akka/akka_basics?id=props-constructor) of an actor.  

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
 * A [lambda expression](learn_to_code/java/java_basics?id=lambda-expression) which basically acts as a stand-in for utilizing the constructor of the given class.


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
   * A [lambda expression](learn_to_code/java/java_basics?id=lambda-expression) which basically acts as a stand-in for utilizing the constructor of the given class.
* More on the lambda function
 * Keep in mind that the sent class serves a dual purpose - it acts as a trigger to our Actor to do something based off the _specific_ class, but it _also_ contains an object of that type that we can interact with (at the very least, read variables from).
 * The lambda function defines _what_ we would like to do once we receive the message.
 * The first part (before the ->) basically turns the sent object (in this case, a <font color="purple">WhoToGreet</font> object) into a variable we can use; now, the variable <font color="purple">wtg</font> represents a passed <font color="purple">WhoToGreet</font> object.
 * The actual function itself; this defines _what_ we want to do once we receive this object type - in this case, we set our own <font color="purple">greeting</font> String to a (previously determined) <font color="purple">message</font> _plus_ the 'who' variable that is inside the sent <font color="purple">WhoToGreet</font> object (i.e. <font color="purple">wtg</font>). we can access <font color="purple">wtg.who</font>, which is a public `String` in the <font color="purple">WhoToGreet</font> class.
   * This can be far more complicated if we wish - the only job 
* The final thing done is `build()` which finished building the 'listener'.

> `match()` is reached by a `tell()`

# Finding An Actor To Message 

## Finding An Actor Created Locally

The obvious way to contact an actor is to save it as an object (an `ActorRef` object to be specific) and then to use [.tell()](learn_to_code/java/akka/akka_basics?id=sending-the-message) to send that actor a message. This was explained [here](learn_to_code/java/akka/akka_basics?id=sending-the-message) and an example is [here](learn_to_code/java/akka/akka_basic_examples?id=akkaquickstartjava). What happens if you are in a different actor, though? The rest of this section will cover that.  

## actorSelection

> [Here](https://petabridge.com/blog/when-should-I-use-actor-selection/) is a good article on when to use ActorSelection.  

If you know an actor's name - or part of its name - you can use <font color="green">actorSelection()</font> to find the actor and send it a [tell()](learn_to_code/java/akka/akka_basics?id=sending-the-message).  When using this, know that:  
* <font color="green">actorSelection()</font> is not limited to finding one actor - it can find _multiple_ actors if your search is not narrow enough, and if you used it in combination with tell(), you may accidentally send a message to a bunch of actors you had no intention of sending a message to.
* <font color="green">actorSelection()</font> does _not_ return an `ActorRef` - and there is no straightforward way to get an `ActorRef` out of it. To get an `ActorRef` you will have to use <font color="green">actorSelection()</font> in combination with something else (a `Future` or the [Identity method](learn_to_code/java/akka/akka_basics?id=the-identity-method).  
* <font color="green">actorSelection()</font> should be used sparingly - it should only be used if you need to send multiple messages at once, if you are using the [Identity method](learn_to_code/java/akka/akka_basics?id=the-identity-method) to get an `ActorRef`, or if you will not need to send tells from the sending class to the specific actor multiple times (if this is the case, you should make an `ActorRef` variable and use the [Identity method](learn_to_code/java/akka/akka_basics?id=the-identity-method) to store the `ActorRef` object).  

An example of <font color="green">actorSelection()</font>:  
```
private ActorSelection myActorSelection = context().actorSelection("someActor");
myActorSelection.tell(new SomeMessageClass(), self());
```  
* Since this uses `context()`, this will only work for children of the current actor.
* `SomeMessageClass` is an example message class - this is any valid [message](learn_to_code/java/akka/akka_basics?id=messages-practical).  

Another example that uses the [entire ActorSystem](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation) to find the actor:  
```
private ActorSelection myActorSelection = context().system().actorSelection("akka://helloakka/user/someActor");
myActorSelection.tell(new SomeMessageClass(), self());
```  
* For more on the naming convention, see [here](learn_to_code/java/akka/akka_basics?id=actor-names-and-paths).  
* Since this uses `context().system()`, the entire actor system is searched - if you have access to the system object you can just use that (so if you use [this example](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation), you could use `system` instead of `context().system()`).   
* `SomeMessageClass` is an example message class - this is any valid [message](learn_to_code/java/akka/akka_basics?id=messages-practical).  

Wildcards (`*`) can be used if you do not know the precise name _or_ you want to select multiple actors to send a [tell()](learn_to_code/java/akka/akka_basics?id=sending-the-message). For example, if there were three actors `A'`, `B`, `C` and _each_ actor had children `X`, `Y`, `Z`, the following code:
```
private ActorSelection myActorSelection = context().system().actorSelection("akka://helloakka/user/*/X");
myActorSelection.tell(new SomeMessageClass(), self());
```  

would send a tell to 3 actors (assuming the system name is `helloakka`):  
* `akka://helloakka/user/A/X`  
* `akka://helloakka/user/B/X`  
* `akka://helloakka/user/C/X`  

Note that the wildcard will _not_ traverse a tree to find children; so for example:  
```
private ActorSelection myActorSelection = context().system().actorSelection("akka://helloakka/user/*");
myActorSelection.tell(new SomeMessageClass(), self());
```  

Would _only_ send a tell to:  
* `akka://helloakka/user/A`  
* `akka://helloakka/user/B`  
* `akka://helloakka/user/C`  

And _not_ to:  
* `akka://helloakka/user/A/X`  
* `akka://helloakka/user/A/Y`  
* `akka://helloakka/user/A/Z`  
* `akka://helloakka/user/B/X`  
* `akka://helloakka/user/B/Y`  
* `akka://helloakka/user/B/Z`  
* `akka://helloakka/user/C/X`  
* `akka://helloakka/user/C/Y`  
* `akka://helloakka/user/C/Z`  

!> Unfortunately, <font color="green">actorSelection()</font> does _not_ return an `ActorRef` and there are situations where you need one. If you need an `ActorRef`, the best method to do this is to use the [Identity method](learn_to_code/java/akka/akka_basics?id=the-identity-method).  

## actorSelection and Await

!> This method is _not_ recommended as it blocks / waits for a response - _only_ use this in odd or one-off situations. Generally speaking you should use the [Identity method](learn_to_code/java/akka/akka_basics?id=the-identity-method).  

If you need an `ActorRef`, you can use a combination of [actorSelection](learn_to_code/java/akka/akka_basics?id=actorselection) and the `Await` method. _Please note_ this combination will block and wait for a response, which can fail.  

To do this you will need to import these libraries:
```
import akka.actor.*;
import akka.util.Timeout;
import scala.concurrent.Await;
import scala.concurrent.duration.Duration;
import scala.concurrent.Future;
```  

And here is the example code:

```
ActorRef myActor = null;
Timeout myTimeout = new Timeout(Duration.create(500, TimeUnit.MILLISECONDS))
ActorSelection selection = context().system().actorSelection("akka://helloakka/user/someAct*");
Future<ActorRef> futureActor = selection.resolveOne(myTimeout);
try {
	myActor = (ActorRef) Await.result(futureActor, myTimeout.duration());
	myActor.tell(new SomeMessageClass(), self());
} catch (Exception e) {

}

```  
* For more on the naming convention, see [here](learn_to_code/java/akka/akka_basics?id=actor-names-and-paths).  
* Since this uses `context().system()`, the entire actor system is searched - if you have access to the system object you can just use that (so if you use [this example](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation), you could use `system` instead of `context().system()`).   
* `SomeMessageClass` is an example message class - this is any valid [message](learn_to_code/java/akka/akka_basics?id=messages-practical).  
* This searches for an actor that begins with `someAct` (lets pretend our actual target actor is named `someActor`).  
* There is a timeout - this is necessary, as since this code will actually block and wait for a response, we need a timeout if that response does not come.  

If the above code is successful, the object `myActor` will store an `ActorRef` that you can use later.  

## The Identity Method  

> If you would like to see an example of the Identity method, you can find one [here](learn_to_code/java/akka/akka_basic_examples?id=finding-an-actor-example).  

Usually you can use [actorSelection](learn_to_code/java/akka/akka_basics?id=actorselection) in a one-off situation to send a tell to an actor; sometimes, however, this is not possible - sometimes you will need to reference the actor multiple times and its not practical to spend the resources using ActorSelection each time; in other cases, you need an ActorRef (and, unfortunately, ActorSelection does not provide an ActorRef). In times like these, you can use the <font color="green">Identity method</font> (i.e. the `Identify` / `ActorIdentity` classes) to get back an actor reference.  

The ultimate goal of this is to get an ActorRef object so we only have to find an actor once.  

This method hinges on sending an `Identify` object to an actor (using ActorSelection), and the actor will respond with an `ActorIdentity` object that contains an ActorRef. All actors respond to an Identify request automatically, so there is no need to write logic for it in the [createReceive()](learn_to_code/java/akka/akka_basics?id=overriding-createreceive) (that said, they do _not_ process `ActorIdentity` automatically so you will have to write the functionality for that).  

Each `Identify()` object has an `int` (or `long`) which is known as parameter `messageID` (also known as the `correlationID`), and its returned to this actor in the `ActorIdentify` object (as `id.correlationId()`). Since its possible that one actor could make _multiple_ requests for different actors, we need a way to quickly identify which request is which - and that is done through `correlationId`. Finally, note that while you can use a primitive int or long here, the `id.correlationId()` will be in its object form (either `Integer` or `Long`).  

There is a walkthrough of these concepts in [the example](learn_to_code/java/akka/akka_basic_examples?id=finding-an-actor-example); you can also check out my [Akka Remote Actor Example](learn_to_code/java/akka/akka_basic_examples?id=remote-actor-example).  

## Passing the ActorRef in Props  

Another way to make another actor's `ActorRef` available to an actor is to simply pass its `ActorRef` along in the [props()](learn_to_code/java/akka/akka_basics?id=props-constructor); note that the referenced actor must be known before the actor that needs access to it is created beforehand _and_ the `ActorRef` is available; that said, if possible, this is a great way to get an `ActorRef` to another actor that needs it.  

# Remote Actors  

> For more on remote actors, see the official [Akka docs on Artery Remoting](https://doc.akka.io/docs/akka/current/remoting-artery.html), and my example is [here](learn_to_code/java/akka/akka_basic_examples?id=remote-actor-example).  

Remoting is a powerful concept that allows a remote system / server / host to send (and receive) messages to an Akka [ActorSystem](learn_to_code/java/akka/akka_basics?id=basic-actorsystem-creation). This can eventually lead to splitting a monolithic piece of code into smaller microservices.

Akka 2.6 moved away from its traditional remoting and moved towards what they call <font color="purple">Artery remoting</font> (which I will refer to as simply '<font color="greent">remoting</font>' from here on out).  Some of the concepts in pre-Akka 2.6 <font color="greent">remoting</font> will not work; there are slight changes to some of the syntax (although the overall concept does more or less stay the same). These notes are for Akka <font color="greent">remoting</font> for version 2.6+.  

If you would like to see an example of a remote actor in action, you can find one [here](learn_to_code/java/akka/akka_basic_examples?id=remote-actor-example).  

## How Remote Actors Works

> An example of a working remote actor system can be found  [here](learn_to_code/java/akka/akka_basic_examples?id=remote-actor-example).  

Remote actors work by host / server `A` obtaining an Akka actor reference to host `B`, which allows host `A` to send [tell()](learn_to_code/java/akka/akka_basics?id=sending-the-message)s to actors on `B`.  This is mainly done via an [actorSelection](learn_to_code/java/akka/akka_basics?id=actorselection), which is launched _from_ `A`'s local actor system but uses `B`'s actor system; this is because the absolute naming convention of the Akka actor [contains an IP and port](learn_to_code/java/akka/akka_basics?id=actor-names-and-paths) - so even though the [actorSelection](learn_to_code/java/akka/akka_basics?id=actorselection) is launched from `A`'s actor system, if `B`s actor system name/IP/port is specified in the parameter of `actorSelection`, it will use `B`'s actor system to find the sought after actor.  This may be a bit confusing, so [take a look at this example](learn_to_code/java/akka/akka_basic_examples?id=remote-actor-example).  

## Additional Maven Libraries (Remoting)  

If you use the [base pom.xml file](learn_to_code/java/akka/akka_basic_examples?id=pomxml) I have for Akka ([here](learn_to_code/java/maven?id=what-is-the-main-idea-behind-maven) is more on Maven), you will need to add at least one more entry for <font color="greent">remoting</font> to the `<dependencies` section:  
```
    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-remote_${akka.scala.version}</artifactId>
      <version>${akka.version}</version>
    </dependency>
```  

In addition, _if_ you are using UDP as a transport, you will also need some additional libraries - Artery UDP depends on Aeron, so you will need to include these so other builds not specifically using Aertery / Aeron can avoid having these in their classpath:  
```
    <dependency>
      <groupId>io.aeron</groupId>
      <artifactId>aeron-driver</artifactId>
      <version>1.26.0</version>
    </dependency>
    <dependency>
      <groupId>io.aeron</groupId>
      <artifactId>aeron-client</artifactId>
      <version>1.26.0</version>
    </dependency>
```  

## Serialization  

It should be noted that remote actors needed [messages](learn_to_code/java/akka/akka_basics?id=messages-practical) to be serialized; this can be done by having the class implement `Serializable` like so:
```
package com.wagenseller;

import java.io.Serializable;

public class StoreData implements Serializable {
    public String someString;

    public StoreData(String someString) {
        this.someString = someString;
    }
}
```  

This will allow the class to be sent as a message remotely, but there are many security risks with this.  Instead of this, you _should_ use something else like Google's Protocol Buffers (my notes on that are [here](learn_to_code/java/protobuf), and the official page is [here](https://developers.google.com/protocol-buffers/)). If you do not want to use Google's Protocol Buffers, there are other suggestions [here](https://manuel.bernhardt.io/2018/07/20/akka-anti-patterns-java-serialization/).  

## application.conf (Remoting)  

> More on application.conf, in general, can be found [here](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf); if you would like to see an example of it in action, check out my example [here](learn_to_code/java/akka/akka_basic_examples?id=remote-actor-example).  

In order for an actor system to be able to accept incoming messages, there must be a few settings defined in [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf). Here are the basics for an Akka system that will accept requests to its own actors:  
```
akka {
  actor {
    provider = remote
    allow-java-serialization = on
    warn-about-java-serializer-usage = off
  }
  remote {
    artery {
      transport = tcp
      canonical {
        hostname = "127.0.0.1"
        port = 25520
      }
    }
  }
}
``` 
* `akka.actor.provider` is set to `remote` (I believe the default is `local`); the publishers of Akka actually recommend using `cluster` over `remote`, but for our purposes we will use `remote`.  
* `akka.actor.allow-java-serialization` and `akka.actor.warn-about-java-serializer-usage` should **not** be set to these values normally; this is to allow for the use of Java [Serialization](learn_to_code/java/akka/akka_basics?id=serialization), but instead of having your message class implement `Serializable` you _should_ use something else like Google's Protocol Buffers (my notes on that are [here](learn_to_code/java/protobuf), and the official page is [here](https://developers.google.com/protocol-buffers/)). If you do not want to use Google's Protocol Buffers, there are other suggestions [here](https://manuel.bernhardt.io/2018/07/20/akka-anti-patterns-java-serialization/).  
* `akka.remote.artery.canonical.hostname` is the external (logical) hostname.  
 * If you do not include this, it will be assumed to be `127.0.1.1` - and the foreign node _will see `127.0.0.1` as this node's address_. This means the foreign node will never be able to return the initial handshake as it will end up trying to send it to itself. **Make sure** this is set to a real value.  
 * There is said to be an `akka.remote.artery.bind.hostname` which is for an internal network (bind) hostname - however, this did not work as I thought it would.  
* `akka.remote.artery.canonical.port` is the external (logical) port.  
 * If you do not set this, it is assumed to be `25520`.  
 * There is said to be an `akka.remote.artery.bind.port` which is for an internal network (bind) port - however, this did not work as I thought it would.  
* `akka.remote.artery.transport` is the transport.  

The `application.conf` on a system that will simply make requests is:  
```
akka {
   actor {
    provider = remote
    allow-java-serialization = on
    warn-about-java-serializer-usage = off
  }
}
```  

!> Note that if you set `akka.remote.artery.canonical.port` = 0, this will _randomize_ the port; usually you need to know what the port will be, **but** sometimes the randomization of the port is good for things like unit tests.  
