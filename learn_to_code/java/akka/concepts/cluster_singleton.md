# Akka Concepts: Cluster Singleton

> Concept (API-agnostic) page. For code see [Classic: Cluster Singleton](/learn_to_code/java/akka/classic/cluster_singleton) and [Typed: Cluster Singleton](/learn_to_code/java/akka/typed/cluster_singleton).  
> Official docs: [Cluster Singleton](https://doc.akka.io/docs/akka/2.6/typed/cluster-singleton.html).  

# What A Cluster Singleton Is

A <font color="purple">Cluster Singleton</font> is an actor for which **exactly one instance exists across the entire cluster at any time**. It is the cluster's single "brain" for whatever it coordinates - a registry, a scheduler, a router, etc.  

If the node hosting the singleton leaves or crashes, Akka starts a **fresh** instance on another node. This is failover, and it is the whole point of the pattern.  

# The Three Moving Parts

Every node runs the same wiring; Akka elects which node actually hosts the live instance.  

* <font color="green">Singleton Manager</font> - one per node. The managers coordinate so the real singleton runs on exactly one node (the **oldest** member). When that node leaves, another manager starts a new instance.  
* <font color="green">Termination message</font> - the message the manager sends the old instance to hand off gracefully during migration (commonly `PoisonPill`).  
* <font color="green">Singleton Proxy</font> - one per node. A stable, node-local `ActorRef` that always forwards to wherever the singleton currently lives (buffering briefly during failover). Clients talk to the **proxy** and never need to know which node hosts the singleton.  

# A New Instance Starts With Empty State

The critical consequence for design: a failed-over singleton is a **brand-new actor with empty in-memory state**. Anything it was tracking must be **rebuilt** from the survivors. See [Rehydration and WakeUp](/learn_to_code/java/akka/concepts/rehydration_and_wakeup) for how.  

# Cost / When To Use

A singleton is a **bottleneck and a single point of (re)start** by definition - all coordination funnels through one actor, and during failover there is a brief window with no instance. Use it for genuinely cluster-global coordination, not for work that could be sharded across nodes.  

# See Also

- [Cluster Sharding](/learn_to_code/java/akka/concepts/cluster_sharding) - the complementary "many instances" pattern.  
- [Rehydration and WakeUp](/learn_to_code/java/akka/concepts/rehydration_and_wakeup)  
- [Split Brain and Quorum](/learn_to_code/java/akka/concepts/split_brain_and_quorum) - why two singletons must never run at once.  
