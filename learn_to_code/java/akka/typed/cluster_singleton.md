# Akka Typed: Cluster Singleton

> Concept: [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton). This page is the **Typed API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# Init (per node)

In Typed there is no separate manager/proxy you wire by hand - the `ClusterSingleton` extension gives
you a single proxy `ActorRef<T>` and manages the rest:

```java
ClusterSingleton singleton = ClusterSingleton.get(system);
ActorRef<Coordinator.Command> proxy = singleton.init(
    SingletonActor.of(Coordinator.create(), "coordinator"));
```

# TODO

- `SingletonActor.withStopMessage(...)` for graceful hand-off.  
- Rebuild-on-start via the behavior's setup (link to [Rehydration](/learn_to_code/java/akka/typed/rehydration_and_wakeup)).  
- Contrast with the Classic manager/proxy wiring.  
