# Akka Typed: Passivation

> Concept: [Passivation](/learn_to_code/java/akka/concepts/passivation). This page is the **Typed API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# Manual Passivation

The entity asks sharding to passivate it by sending a `Passivate` to its shard via the entity
context (you typically keep a `stopMessage` and send `Passivate` when idle):

```java
Entity.of(typeKey, ctx -> Printer.create(ctx))
    .withStopMessage(Printer.GoodBye.INSTANCE);
// ...inside the behavior, request passivation:
ctx.getShard().tell(new ClusterSharding.Passivate<>(ctx.getSelf()));
```

# Automatic (Idle) Passivation

Same `application.conf` keys as Classic (passivation is a sharding feature, not an API feature):

```hocon
akka.cluster.sharding.passivation.strategy = none   # disable, newer 2.6
```

# TODO

- `remember-entities` in Typed.  
- Worked example.  
