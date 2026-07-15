# Akka Classic: Serialization and Actor Refs

> Concept: [Serialization and Actor Refs](/learn_to_code/java/akka/concepts/serialization_and_actor_refs). This page is the **Classic API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# Encoding A Ref As A String (the right way)

```java
// CORRECT - address-qualified, resolves on any node
String path = Serialization.serializedActorPath(jobRef);

// WRONG - omits @host:port for a local ref; resolves to a dead local phantom on another node
String bad = jobRef.path().toSerializationFormat();
```

# Resolving It Back

```java
ActorRef ref = ((ExtendedActorSystem) getContext().getSystem())
        .provider().resolveActorRef(path);
getContext().watch(ref);   // safe only if `path` was address-qualified
```

# TODO

- The death-watch-fires-immediately failure walkthrough.  
- Prefer passing the `ActorRef` in the message instead.  
- Worked code from the print-shop example.  
