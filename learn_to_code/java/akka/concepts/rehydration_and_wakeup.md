# Akka Concepts: Rehydration and WakeUp

> Concept (API-agnostic) page. For code see [Classic: Rehydration and WakeUp](/learn_to_code/java/akka/classic/rehydration_and_wakeup) and [Typed: Rehydration and WakeUp](/learn_to_code/java/akka/typed/rehydration_and_wakeup).  

# The Problem

A failed-over [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton) is a **brand-new actor with empty in-memory maps**. Whatever it was tracking - registrations, an index of in-flight work - is gone on the new instance. That state has to be **rebuilt from the survivors** without losing track of work that is still running.  

<font color="purple">Rehydration</font> is the pattern for rebuilding that state.  

# The Two Cooperating Mechanisms

## 1. preStart WakeUp Broadcast

`preStart` runs **every time a new singleton instance starts**, including the one created after a failover. So it is the right hook to (re)build cluster-wide state. On start, the new singleton broadcasts a <font color="purple">WakeUp</font> message to every entity it is responsible for.  

* On a **fresh** cluster, WakeUp *creates* the sharded entities (first message → on-demand creation) and they register.  
* After a **failover**, WakeUp reaches *already-running* entities. They treat it as the cue to **replay their state** (registration + every job/unit of work they currently own) back into the new, empty singleton.  

The entity makes WakeUp **idempotent**: it distinguishes a *first* WakeUp ("power on and register") from a *re-*WakeUp ("rehydrate everything I have").  

## 2. Liveness Self-Heal Timer

A periodic timer on the singleton re-WakeUps any configured entity that is **not currently registered**. This revives an entity that died or [rebalanced](/learn_to_code/java/akka/concepts/cluster_sharding) (or [passivated](/learn_to_code/java/akka/concepts/passivation)) *after* boot - without needing a singleton restart.  

> **Target only the missing ones.** A WakeUp to an *already-registered* entity makes it re-run rehydration (re-send all its work). Blanket-WakeUp-ing everyone on a timer would cause a needless storm, so the liveness check skips healthy entities and only pokes the ones it cannot see.  

# Why This Models "Automatic Backup Takes Over"

Put together: the primary singleton dies, a new one starts empty, broadcasts WakeUp, and every survivor replays its registration and live work - so the new brain's index and registry are rebuilt without losing in-flight work. That is rehydration, and it is what makes singleton failover safe rather than amnesiac.  

# WakeUp Rebuilds The Singleton's View, Not A Reborn Entity's Own State

Keep two very different failures separate - they both involve WakeUp but recover different things:  

* <font color="green">The singleton failed over (entities still alive)</font> - the case above. Each surviving entity is **fully intact in memory**, so on re-WakeUp it **replays its own state** (registration + live work) into the new, empty singleton. Nothing was lost because the source of truth - the entity - never went away.  
* <font color="red">The entity itself was recreated</font> (crashed with its node, [rebalanced](/learn_to_code/java/akka/concepts/cluster_sharding), or [passivated](/learn_to_code/java/akka/concepts/passivation)) - the new incarnation comes back **blank**: it re-runs its constructor / `preStart` and its in-memory state is **gone**. A WakeUp here re-registers it, but it has **nothing to replay** - it cannot rehydrate state it no longer holds.  

So WakeUp/rehydration restores the **singleton's view of the world from the entities**, not an entity's own lost state. A reborn entity only comes back with what it can reconstruct from scratch: its **entity id** (it knows this from its actor name) plus whatever its factory hard-codes. Anything else - the work it was tracking, parameters from earlier messages - is lost unless it was made **durable** (persisted to the entity's own event/state journal) or is **re-derivable from the id** (looked up from an external store on startup).  

> **Direction of truth.** The entity is the source of truth for its own work, and the singleton is rebuilt *from* the entities - never the reverse. That is exactly why a reborn entity loses everything: there is nothing more authoritative to restore it from.  

# See Also

- [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton)  
- [Passivation](/learn_to_code/java/akka/concepts/passivation) - a common reason an entity silently drops off and needs re-WakeUp.  
- [Split Brain and Quorum](/learn_to_code/java/akka/concepts/split_brain_and_quorum)  
