# Akka Typed: Serialization and Actor Refs

> Concept: [Serialization and Actor Refs](/learn_to_code/java/akka/concepts/serialization_and_actor_refs). This page is the **Typed API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# Prefer Passing The Typed Ref

In Typed you usually pass an `ActorRef<T>` **inside the message** and let the configured serializer
(typically Jackson) handle it - Akka encodes it with its full address automatically. This is the
strongly-typed, footgun-free path and should be your default.

```java
// message carries a reply-to ref; Akka serializes it address-qualified across nodes
public record StartJob(String jobId, ActorRef<JobEvent> replyTo) {}
```

# If You Must Hand-Encode

The same rule as Classic applies if you drop to string paths: use
`Serialization.serializedActorPath(ref)` (address-qualified), never `path().toSerializationFormat()`.

# TODO

- Jackson serializer config for typed messages.  
- `ActorRefResolver` for typed refs (the typed analogue of `resolveActorRef`).  
- Worked example.  
