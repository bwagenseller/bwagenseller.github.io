# Akka Concepts: Passivation

> Concept (API-agnostic) page. For code see [Classic: Passivation](/learn_to_code/java/akka/classic/passivation) and [Typed: Passivation](/learn_to_code/java/akka/typed/passivation).  
> Official docs: [Cluster Sharding - Passivation](https://doc.akka.io/docs/akka/2.6/typed/cluster-sharding.html#passivation).  

# What Passivation Is

<font color="purple">Passivation</font> is the act of **stopping an idle sharded entity actor to free memory**, with the understanding that [Cluster Sharding](/learn_to_code/java/akka/concepts/cluster_sharding) will **recreate it on demand** the next time a message is routed to that entity id.  

It is purely a memory-management optimization. From the *outside* nothing changes: you still address the entity by its id, and a message always reaches a live instance - sharding just may have to spin a fresh one up first (re-running its constructor / `preStart`).  

## It Only Exists In Sharding

This is the key filing point: passivation is a **Cluster Sharding** concept, not a general clustering one.  

* [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton) does **not** passivate - there is exactly one and it is always wanted alive.  
* Plain remoting / membership has no notion of it.  
* Only **sharded entities** passivate, because only sharding owns a (potentially huge) population of entity actors whose lifecycle it manages.  

# Why Idle Entities Get Stopped

Sharding creates an entity lazily on first message and keeps it resident. With millions of possible entity ids (one per user, per device, per call...), keeping every entity that was ever touched resident would exhaust the heap. Passivation caps that: entities you have stopped talking to quietly go away and reappear if needed.  

# The Idle Clock Resets On Every Message

An entity is "idle" only while its mailbox stays empty. **Every message it receives resets the idle timer.** So an entity that is regularly used never passivates; only one that genuinely goes quiet for the whole timeout window does.  

# Automatic (Idle) Passivation

Akka can passivate entities automatically after an idle timeout. The shard region logs the active strategy at startup, e.g.:  

```
ShardRegion ... PrinterAgent: Automatic entity passivation: idle entities after [2.000 min], checked every [1.000 min]
```

Decoded:  

* <font color="green">idle entities after [2.000 min]</font> - an entity with no messages for 2 minutes becomes eligible.  
* <font color="green">checked every [1.000 min]</font> - the region sweeps once a minute, so an entity is actually stopped somewhere between 2 and 3 minutes after its last message.  

> **Config drift warning:** the key changed across Akka 2.6 minor versions.  
> * Older 2.6: `akka.cluster.sharding.passivate-idle-entity-after = 120s` (or `off`).  
> * Newer 2.6: the `akka.cluster.sharding.passivation.*` strategy block; disable with `akka.cluster.sharding.passivation.strategy = none`.  
> The log wording above ("idle entities after ... checked every ...") corresponds to the newer strategy form.  

# Passivate vs PoisonPill

Do **not** stop a sharded entity with `PoisonPill` when you mean "I'm idle". Use the dedicated <font color="purple">Passivate</font> message instead.  

* `Passivate` tells the shard region "stop me, and **do not treat this as a crash**" - and critically, the region **buffers** any messages that arrive for the entity during shutdown and **redelivers** them to the fresh incarnation, so nothing is lost.  
* A bare `PoisonPill` stops the actor but sharding may just recreate it on the next message anyway. There is no "stop a sharded entity forever" button - "forever" only means "and stop sending it messages".  

# remember-entities

By default a passivated entity stays gone until *something* messages it. With `akka.cluster.sharding.remember-entities = on`, sharding persists the set of active entities and **automatically restarts** them after a rebalance or node crash, rather than waiting for a message. This trades extra persistence/overhead for faster reactivation.  

# Does A Message Mid-Window Re-WakeUp The Entity?

It depends on whether the entity was **still alive or already stopped** when the message lands - which is exactly why the idle clock matters.  

Using the *idle after 2 min, checked every 1 min* strategy above:  

* <font color="green">Message arrives while the entity is still resident</font> - it just **resets the idle timer**. No passivation, no rehydration; the actor was alive the whole time and simply handles the message.  
* <font color="green">Message arrives after the entity was passivated</font> - sharding **creates a fresh instance on demand** to receive it. *That* recreation is the WakeUp / rehydration.  

So a time like "minute 3" is genuinely ambiguous: with the actual stop happening somewhere between minute 2 and 3, a message at minute 3 might land just before the sweep stops it (timer reset, stays alive) or just after (recreated). The rule of thumb: **rehydration only happens to an entity that is actually gone** - a message to a living entity just resets its clock.  

# Passivation Is Not Required For WakeUp / Rehydration

The relationship is **one-directional**. Passivation is *one* trigger that can lead to a [Rehydration / WakeUp](/learn_to_code/java/akka/concepts/rehydration_and_wakeup), but rehydration is the general recovery path and has **other** triggers too:  

* <font color="purple">Node crash</font> - the hosting node dies; sharding recreates the entity elsewhere.  
* <font color="purple">Rebalance</font> - sharding relocates shards across nodes; entities stop on the old node and reappear on the new one.  
* <font color="purple">Full cluster / JVM restart</font>.  
* Even a bare `PoisonPill` - sharding may recreate the entity on the next message anyway.  

So `passivation -> WakeUp` holds, but a WakeUp does **not** imply passivation happened. Passivation is *sufficient* to trigger a WakeUp, not *necessary* for one.  

# Interaction With Liveness Self-Heal

Passivation interacts with the [Rehydration / WakeUp](/learn_to_code/java/akka/concepts/rehydration_and_wakeup) and liveness patterns. In the print-shop example, a printer entity that sits idle long enough gets passivated (losing its in-memory registration); the coordinator's periodic liveness check then notices it is no longer registered and re-`WakeUp`s it. So an unexpected re-WakeUp after a quiet period is usually passivation at work, not a bug.  

# See Also

- [Cluster Sharding](/learn_to_code/java/akka/concepts/cluster_sharding)  
- [Rehydration and WakeUp](/learn_to_code/java/akka/concepts/rehydration_and_wakeup)  
- [Clustering (main page)](/learn_to_code/java/akka/clustering)  
