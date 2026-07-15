# Akka Typed: Cluster Sharding

> Concept: [Cluster Sharding](/learn_to_code/java/akka/concepts/cluster_sharding). This page is the **Typed API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# Init An Entity Type

```java
ClusterSharding sharding = ClusterSharding.get(system);
EntityTypeKey<Printer.Command> typeKey =
    EntityTypeKey.create(Printer.Command.class, "PrinterAgent");

sharding.init(Entity.of(typeKey, ctx ->
    Printer.create(ctx.getEntityId())));     // entity learns its id from the context
```

# Sending To An Entity

```java
EntityRef<Printer.Command> ref = sharding.entityRefFor(typeKey, "printer-A");
ref.tell(new Printer.StartJob(...));
```

# TODO

- No hand-written MessageExtractor - the EntityTypeKey + envelope handle routing.  
- Worked example.  
- Cross-reference [Passivation](/learn_to_code/java/akka/typed/passivation).  
