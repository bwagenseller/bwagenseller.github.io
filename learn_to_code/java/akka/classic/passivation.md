# Akka Classic: Passivation

> Concept: [Passivation](/learn_to_code/java/akka/concepts/passivation). This page is the **Classic API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# Manual Passivation

A classic entity asks to be passivated by sending its parent (the shard region) a `Passivate`
wrapping a stop message:

```java
getContext().getParent().tell(
    new ShardRegion.Passivate(PoisonPill.getInstance()), getSelf());
```

The shard region buffers incoming messages during shutdown and redelivers them to the fresh
incarnation, so nothing is lost.

# Automatic (Idle) Passivation

Configured in `application.conf` - note the key drifted across 2.6 minor versions:

```hocon
# newer 2.6 strategy form
akka.cluster.sharding.passivation.strategy = none   # disable
# older 2.6 form
akka.cluster.sharding.passivate-idle-entity-after = 120s   # or off
```

# TODO

- `remember-entities` example.  
- Passivate vs PoisonPill demonstration.  
- Worked code from the print-shop example.  
