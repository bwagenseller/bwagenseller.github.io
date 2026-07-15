# Akka Classic: Cluster Singleton

> Concept: [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton). This page is the **Classic API** implementation.  
> <font color="red">STUB - to be filled in.</font>  

# Wiring (per node)

Each node starts a singleton **manager** and a singleton **proxy**:

```java
// MANAGER - one per node; Akka runs the real actor on the oldest node only.
system.actorOf(
    ClusterSingletonManager.props(
        MyCoordinator.props(),
        PoisonPill.getInstance(),                       // hand-off / termination message
        ClusterSingletonManagerSettings.create(system)),
    "coordinatorSingleton");

// PROXY - one per node; stable local ref that forwards to wherever the singleton lives.
ActorRef proxy = system.actorOf(
    ClusterSingletonProxy.props(
        "/user/coordinatorSingleton",
        ClusterSingletonProxySettings.create(system)),
    "coordinatorProxy");
```

# TODO

- preStart as the failover rebuild hook (link to [Rehydration](/learn_to_code/java/akka/classic/rehydration_and_wakeup)).  
- Talking to the singleton via `actorSelection("/user/coordinatorProxy")` from entities.  
- Worked code from the print-shop example.  
