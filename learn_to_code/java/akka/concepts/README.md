<img
    src="./assets/img/VaultBoy.png"
    width="88"
    style="display: block; width: 88px; margin: auto; margin-bottom: 1em"
/><span style="display: block; text-align: center; font-size: 1.75em;"> Akka Concepts </span>

# Java
- [Back To Java](/learn_to_code/java/) - Go back to the Java page.

# Akka
- [Back To Akka](/learn_to_code/java/akka/) - Back to Akka.

# Akka Concepts
These pages cover the **API-agnostic ideas** behind Akka clustering - the *what* and *why*, with no
code. Each concept is implemented two ways: see the [Classic](/learn_to_code/java/akka/classic/) and
[Typed](/learn_to_code/java/akka/typed/) branches for the *how*.

- [Cluster Singleton](/learn_to_code/java/akka/concepts/cluster_singleton) - One actor for the whole cluster; failover.
- [Cluster Sharding](/learn_to_code/java/akka/concepts/cluster_sharding) - Entities distributed across nodes.
- [Passivation](/learn_to_code/java/akka/concepts/passivation) - Stopping idle sharded entities to free memory.
- [Rehydration and WakeUp](/learn_to_code/java/akka/concepts/rehydration_and_wakeup) - Rebuilding a new singleton's state after failover.
- [Serialization and Actor Refs](/learn_to_code/java/akka/concepts/serialization_and_actor_refs) - Address-qualified refs and the cross-node footgun.
- [Split Brain and Quorum](/learn_to_code/java/akka/concepts/split_brain_and_quorum) - Why you run an odd number of nodes.
