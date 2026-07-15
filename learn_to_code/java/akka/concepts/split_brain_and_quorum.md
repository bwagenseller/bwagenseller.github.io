# Akka Concepts: Split Brain and Quorum

> Concept (API-agnostic) page. This is mostly **configuration + cluster math**, so there is no separate per-API code branch - the same `application.conf` settings apply to Classic and Typed alike.  
> Official docs: [Split Brain Resolver](https://doc.akka.io/docs/akka/2.6/split-brain-resolver.html).  

# The Problem

If the network <font color="purple">partitions</font> (some nodes can no longer reach others), each side sees the other as "unreachable" and **cannot tell a crash from a partition**. If both sides keep running, each may elect its own [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton) - now you have **two brains** with diverging state. That is <font color="purple">split brain</font>, and it is the failure the resolver exists to prevent.  

# The Split Brain Resolver (SBR)

Akka 2.6 ships a <font color="purple">Split Brain Resolver</font>. Enable it with:  

```hocon
akka.cluster.downing-provider-class = "akka.cluster.sbr.SplitBrainResolverProvider"
```

On a partition, SBR applies a deterministic strategy (default: **keep majority**) so that **at most one side survives** and the other **downs itself** - guaranteeing the singleton stays singular. There is a `stable-after` window (it waits for membership to settle before deciding), which is why failover is not instantaneous.  

# Why An Odd Number >= 3

Failover only works if a **majority survives**.  

* **2-node cluster:** kill one node and the survivor is 1 of 2 - **not** a majority. It cannot distinguish "the other crashed" from "we got partitioned," so SBR makes it **shut itself down** rather than risk two independent singletons. You will see something like `SBR took decision DownReachable ... downing [...] including myself`.  
* **3-node cluster:** kill one node and 2 of 3 survive - a majority. The survivors stay up and the singleton cleanly migrates.  

> **General rule:** run an **odd number of nodes, >= 3**, so a single failure still leaves a quorum. Odd numbers avoid a 50/50 split where neither side has a majority.  

# See Also

- [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton)  
- [Rehydration and WakeUp](/learn_to_code/java/akka/concepts/rehydration_and_wakeup) - what the *surviving* side does after the singleton migrates.  
- [Clustering (main page)](/learn_to_code/java/akka/clustering)  
