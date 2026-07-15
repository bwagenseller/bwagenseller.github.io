# Akka Concepts: Cluster Sharding

> Concept (API-agnostic) page. For code see [Classic: Cluster Sharding](/learn_to_code/java/akka/classic/cluster_sharding) and [Typed: Cluster Sharding](/learn_to_code/java/akka/typed/cluster_sharding).  
> Official docs: [Cluster Sharding](https://doc.akka.io/docs/akka/2.6/typed/cluster-sharding.html).  

# What Cluster Sharding Is

<font color="purple">Cluster Sharding</font> distributes a large population of actors - called <font color="purple">entities</font> - across the nodes of a cluster, with **exactly one instance per entity id** living somewhere in the cluster at a time. It is the complement of the [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton): a singleton is "one for the whole cluster", sharding is "one *each*, spread out".  

You address an entity by its **id**, not by an `ActorRef`. Sharding routes the message to whatever node currently hosts that entity, creating the entity on demand if it does not yet exist.  

# Key Terms

* <font color="green">Entity</font> - a single sharded actor instance, identified by a stable entity id (e.g. a user id, a printer id).  
* <font color="green">Shard</font> - a bucket of entities. Entities are grouped into shards; shards (not individual entities) are the unit of distribution and rebalancing.  
* <font color="green">Shard Region</font> - the per-node component you send messages to. It figures out the shard, finds the hosting node, and forwards.  
* <font color="green">Message Extractor</font> - the function that, given a message, produces `(entityId, shardId, payload)`. This is how sharding knows where a message goes.  
* <font color="green">Coordinator</font> - a (singleton) component that decides which node owns which shard and drives rebalancing.  

# On-Demand Creation

An entity does not exist until the first message addressed to its id arrives. Sharding creates it then. In classic sharding the entity actor is **named after its entity id**, so an entity can learn its own id from its actor name.  

# Rebalancing

To keep load even (and to recover when a node leaves), sharding moves whole shards between nodes - <font color="purple">rebalancing</font>. An entity may therefore be stopped on one node and recreated on another during its life. Design consequence: an entity's `ActorRef` is **not** stable; always address by id, and expect in-memory state to be rebuildable.  

# Passivation

Idle entities are stopped to free memory and recreated on demand later. This is its own topic: see [Passivation](/learn_to_code/java/akka/concepts/passivation).  

# remember-entities

With `remember-entities = on`, sharding persists the active entity set and restarts entities automatically after a rebalance or crash, instead of waiting for the next message. See [Passivation](/learn_to_code/java/akka/concepts/passivation#remember-entities).  

# See Also

- [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton)  
- [Passivation](/learn_to_code/java/akka/concepts/passivation)  
- [Serialization and Actor Refs](/learn_to_code/java/akka/concepts/serialization_and_actor_refs) - matters when an entity on one node hands a ref to a coordinator on another.  
