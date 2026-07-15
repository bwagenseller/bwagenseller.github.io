# Akka Classic: Cluster Sharding

> Concept: [Cluster Sharding](/learn_to_code/java/akka/concepts/cluster_sharding). This page is the **Classic API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# Starting A Shard Region

```java
ActorRef region = ClusterSharding.get(system).start(
    "PrinterAgent",                          // type name
    PrinterAgent.props(),                    // entity Props
    ClusterShardingSettings.create(system),
    new PrinterMessageExtractor(NUMBER_OF_SHARDS));
```

# Message Extractor

A classic `ShardRegion.MessageExtractor` (or `HashCodeMessageExtractor`) maps each message to
`(entityId, shardId, payload)`. The entity actor is named after its entity id, so inside the entity
`getSelf().path().name()` is the id.

# TODO

- Full `MessageExtractor` example.  
- On-demand creation + the `getSelf().path().name()` id trick.  
- Worked code from the print-shop example.  
- Cross-reference [Passivation](/learn_to_code/java/akka/classic/passivation).  
