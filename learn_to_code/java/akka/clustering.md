# Akka: Clustering

# Clustering Basics

> For more on how clustering works, see the [Akka documents](http://doc.akka.io/docs/akka/snapshot/scala/cluster-usage.html).  

Akka can utilize [clustering](learn_to_code/computer_science_concepts?id=clustering) in order to help scale the application and mitigate [node](learn_to_code/computer_science_concepts?id=clustering) failures. 


## Node Monitoring 

Akka monitors all nodes in its cluster; however, no sigle node monitors all other nodes (as that becomes impractical once there are dozens - or hundreds - of [nodes](learn_to_code/computer_science_concepts?id=clustering)). Usually, one [node](learn_to_code/computer_science_concepts?id=clustering) will monitor a few of its immediate neighbors via a heartbeat mechanism (the default number of nodes a single node will monitor itself is 5 [nodes](learn_to_code/computer_science_concepts?id=clustering)). If a node cannot be reached, it is considered unavailable to all nodes in the cluster.

This method of node monitoring is 'eventually consistent' and is known as <font color="purple">gossip protocol</font> or <font color="purple">epidemic protocol</font>.  



# Unfiled

* 'Akka Cluster Aware routers' allow work to be distributed across a cluster - for example, if 4 tasks come in for task 'A'. the router will send 2 to server 'Y' and two to server 'Z'.  

* 'Akka Cluster Sharding' distributes actors across the cluster.    

* 'Akka Distributed Data' provides local, replicated, in-memory data storage; access is very fast.  
 * It asynchronously replicates to other nodes, ensuring all nodes have access to the data.  
 
* Akka clusters links multiple Actor systems together into a single cluster of actor systems.  

* **All actor systems in the cluster _must_ have the same name.**  

* The setup in `application.conf` is, at its base, the exact same as remote, with the exception of it being a 'cluster'.  

* Each instance that connects to the actor system cluster is called a <font color="purple">member</font> or <font color="purple">node</font>.

* UIDs
 * A <font color="green">uid</font> is a unique identifier for each hostname and port.  
 * Each time the node is re-started, a new uid is generated.  
 * A hostname / port / uid can only join a cluster _once_.  

* Seed nodes
 * <font color="green">Seed nodes</font> are an initial set of contact points for a cluster.  
 * <font color="green">Seed nodes</font> are simply an 'in' to a cluster.  
 * They are not special in any way - any node can be a <font color="green">seed node</font>.  
 * Once joined to the cluster, the <font color="green">seed nodes</font> are no longer used.  
 * It seems the _same_ seed node list is used by _all_ nodes in the cluster.
 * Its best to have at least 3 <font color="green">seed nodes</font>; I would think its better to have at least 1 geodiverse.  
 * Example of seed nodes in `application.conf`:  
```
akka {
  cluster { 
    seed-nodes = [
	 "akka://SomeActorSystem@127.0.0.1:2550", 
	 "akka://SomeActorSystem@127.0.0.1:2551", 
	 "akka://SomeActorSystem@127.0.0.1:2552"	 
	]
  }
}
``` 
 * <font color="green">Seed nodes</font> have _their own_ actor system name, hostname, and port listed as the _first_ <font color="green">seed node</font> in the list.  
 * You can skip <font color="green">seed nodes</font> if you set up <font color="purple">Akka cluster bootstrap</font> (which determines the <font color="green">seed nodes</font> dynamically), but that is a more complex process that requires additional resources.  
 * The first node in a <font color="green">seed node</font> list is special - this is called the <font color="purple">first node</font>.  
   * _If_ the first node attempts to join and the cluster has not been created, it will create a new cluster with one node.  
   * _If_ the first node attempts to join and the cluster has already been created, it will simply join the cluster.  
   * Everything that is not a first node _requires_ a cluster and cannot create a cluster on its own.  
   * Once the cluster is formed, the first node is no longer relevant - even if its terminated, the cluster will live on.  
 * It is possible that _all_ seed nodes are re-started at the same time - this would mean that any remaining cluster members still running would be an unreachable island, and when the <font color="green">seed nodes</font> come back online, you could have two running clusters.  
   * You can avoid this with a <font color="purple">Akka cluster bootstrap</font>.  
* You can use a 'cluster' as you would a remote, although you have to do a a few things:  
 * You must import the cluster dependency in the pom:  
```
    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-cluster_${akka.scala.version}</artifactId>
      <version>${akka.version}</version>
    </dependency>
```  
 * You need to use 'cluster' instead of 'remote'.  
 * You have to import `import akka.stream.Materializer;` and then create a materializer around the actor system (i.e. `Materializer materializer = Materializer.createMaterializer(system);`  _immediately_ after `system` is created.  
 * Note that this behaves like remote when using actorSelection - you _must_ include the IP and port.  
   * That said, it appears to handle heartbeats and connections better.  
   

Logs  
* When a seed starts a cluster it will look like this:
```
[INFO] [04/17/2020 15:48:31.165] [main] [ArteryTcpTransport(akka://VandelayIndustries)] Remoting started with transport [Artery tcp]; listening on address [akka://VandelayIndustries@127.0.0.1:25520] with UID [4653525310568256145]
[INFO] [04/17/2020 15:48:31.182] [main] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Starting up, Akka version [2.6.4] ...
[INFO] [04/17/2020 15:48:31.338] [main] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Registered cluster JMX MBean [akka:type=Cluster]
[INFO] [04/17/2020 15:48:31.338] [main] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Started up successfully
[WARN] [04/17/2020 15:48:31.697] [VandelayIndustries-akka.remote.default-remote-dispatcher-8] [akka.stream.Log(akka://VandelayIndustries/system/Materializers/StreamSupervisor-1)] [outbound connection to [akka://VandelayIndustries@127.0.0.1:25522], control stream] Upstream failed, cause: StreamTcpException: Tcp command [Connect(127.0.0.1:25522,None,List(),Some(5000 milliseconds),true)] failed because of java.net.ConnectException: Connection refused
[WARN] [04/17/2020 15:48:31.697] [VandelayIndustries-akka.remote.default-remote-dispatcher-5] [akka.stream.Log(akka://VandelayIndustries/system/Materializers/StreamSupervisor-1)] [outbound connection to [akka://VandelayIndustries@127.0.0.1:25521], control stream] Upstream failed, cause: StreamTcpException: Tcp command [Connect(127.0.0.1:25521,None,List(),Some(5000 milliseconds),true)] failed because of java.net.ConnectException: Connection refused
[WARN] [04/17/2020 15:48:31.697] [VandelayIndustries-akka.remote.default-remote-dispatcher-9] [akka.stream.Log(akka://VandelayIndustries/system/Materializers/StreamSupervisor-1)] [outbound connection to [akka://VandelayIndustries@127.0.0.1:25521], message stream] Upstream failed, cause: StreamTcpException: Tcp command [Connect(127.0.0.1:25521,None,List(),Some(5000 milliseconds),true)] failed because of java.net.ConnectException: Connection refused
[WARN] [04/17/2020 15:48:31.697] [VandelayIndustries-akka.remote.default-remote-dispatcher-10] [akka.stream.Log(akka://VandelayIndustries/system/Materializers/StreamSupervisor-1)] [outbound connection to [akka://VandelayIndustries@127.0.0.1:25522], message stream] Upstream failed, cause: StreamTcpException: Tcp command [Connect(127.0.0.1:25522,None,List(),Some(5000 milliseconds),true)] failed because of java.net.ConnectException: Connection refused
[INFO] [akkaMemberChanged][04/17/2020 15:48:36.510] [VandelayIndustries-akka.actor.internal-dispatcher-3] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Node [akka://VandelayIndustries@127.0.0.1:25520] is JOINING itself (with roles [dc-default]) and forming new cluster
[INFO] [04/17/2020 15:48:36.512] [VandelayIndustries-akka.actor.internal-dispatcher-3] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - is the new leader among reachable nodes (more leaders may exist)
[INFO] [akkaMemberChanged][04/17/2020 15:48:36.518] [VandelayIndustries-akka.actor.internal-dispatcher-3] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Leader is moving node [akka://VandelayIndustries@127.0.0.1:25520] to [Up]
[WARN] [04/17/2020 15:48:51.514] [VandelayIndustries-akka.remote.default-remote-dispatcher-5] [Association(akka://VandelayIndustries)] Outbound control stream to [akka://VandelayIndustries@127.0.0.1:25521] failed. Restarting it. akka.remote.artery.OutboundHandshake$HandshakeTimeoutException: Handshake with [akka://VandelayIndustries@127.0.0.1:25521] did not complete within 20000 ms
[WARN] [04/17/2020 15:48:51.514] [VandelayIndustries-akka.remote.default-remote-dispatcher-9] [Association(akka://VandelayIndustries)] Outbound control stream to [akka://VandelayIndustries@127.0.0.1:25522] failed. Restarting it. akka.remote.artery.OutboundHandshake$HandshakeTimeoutException: Handshake with [akka://VandelayIndustries@127.0.0.1:25522] did not complete within 20000 ms
```  
* When another node joins, this is what it will look like from the leader of the cluster's perspective:
```
[INFO] [04/17/2020 15:51:14.587] [VandelayIndustries-akka.actor.internal-dispatcher-2] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Received InitJoin message from [Actor[akka://VandelayIndustries@127.0.0.1:25521/system/cluster/core/daemon/joinSeedNodeProcess-1#-715552654]] to [akka://VandelayIndustries@127.0.0.1:25520]
[INFO] [04/17/2020 15:51:14.588] [VandelayIndustries-akka.actor.internal-dispatcher-2] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Sending InitJoinAck message from node [akka://VandelayIndustries@127.0.0.1:25520] to [Actor[akka://VandelayIndustries@127.0.0.1:25521/system/cluster/core/daemon/joinSeedNodeProcess-1#-715552654]] (version [2.6.4])
[INFO] [akkaMemberChanged][04/17/2020 15:51:14.669] [VandelayIndustries-akka.actor.internal-dispatcher-2] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Node [akka://VandelayIndustries@127.0.0.1:25521] is JOINING, roles [dc-default]
[INFO] [akkaMemberChanged][04/17/2020 15:51:15.602] [VandelayIndustries-akka.actor.internal-dispatcher-6] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25520] - Leader is moving node [akka://VandelayIndustries@127.0.0.1:25521] to [Up]
```  
 * And this is what it looks like from the new node:  
```
[INFO] [04/17/2020 15:51:14.092] [main] [ArteryTcpTransport(akka://VandelayIndustries)] Remoting started with transport [Artery tcp]; listening on address [akka://VandelayIndustries@127.0.0.1:25521] with UID [-4169003783311200650]
[INFO] [04/17/2020 15:51:14.108] [main] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25521] - Starting up, Akka version [2.6.4] ...
[INFO] [04/17/2020 15:51:14.235] [main] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25521] - Registered cluster JMX MBean [akka:type=Cluster]
[INFO] [04/17/2020 15:51:14.235] [main] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25521] - Started up successfully
[INFO] [04/17/2020 15:51:14.655] [VandelayIndustries-akka.actor.internal-dispatcher-6] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25521] - Received InitJoinAck message from [Actor[akka://VandelayIndustries@127.0.0.1:25520/system/cluster/core/daemon#365194569]] to [akka://VandelayIndustries@127.0.0.1:25521]
[INFO] [04/17/2020 15:51:14.699] [VandelayIndustries-akka.actor.internal-dispatcher-3] [Cluster(akka://VandelayIndustries)] Cluster Node [akka://VandelayIndustries@127.0.0.1:25521] - Welcome from [akka://VandelayIndustries@127.0.0.1:25520]
```  

Cluster Management  
* Akka provides <font color="green">Akka HTTP Cluster Management</font>, which gives a set of HTTP endpoints to help manage the cluster.  This can:
 * View cluster sharding distribution.  
 * View the status of the overall cluster.  
 * Remove members from the cluster.  
* To enable <font color="green">Akka HTTP Cluster Management</font>, add this to `application.conf`:  
```
akka {
  management { 
    http {
	  hostname = "127.0.0.1"  
	  port = 8558  
	  route-providers-read-only = false
	}
  }
}
```  
 * The port _must_ be different than the cluster port.  
 * `8558` is the standard akka http port.  
 * `route-providers-read-only = false` is needed if you want to make changes; the default is 'true', which means you can view things but not make changes.
   * The default is for security - only set equal to false if you are behind a firewall or have otherwise put security in place.  
 * If you want more security, you can also use authentication and SSL.  
* You must add a line to the Maven pom.xml file:  
```
    <dependency>
      <groupId>com.lightbend.akka.management</groupId>
      <artifactId>akka-management-cluster-http_${akka.scala.version}</artifactId>
      <version>1.0.6</version>
    </dependency>
```  
 * The version is _not_ consistent with the overall Akka version so you _must_ specify it differently - [here](https://mvnrepository.com/artifact/com.lightbend.akka.management/akka-management-cluster-http) are the different Maven versions.  
* You must also run some java code to use <font color="green">Akka HTTP Cluster Management</font> - you must specifically start the http server.  
 * You must include this class: `import akka.management.javadsl.AkkaManagement;`  
 * To do so, run this _after_ the actor system has been created: `AkkaManagement.get(system).start();`  
 * When run properly, this will show in the logs:  
```
[INFO] [04/18/2020 12:33:07.343] [main] [AkkaManagement(akka://VandelayIndustries)] Binding Akka Management (HTTP) endpoint to: 127.0.0.1:8558
```  
* It seems there is no nice interface - it simply prints JSON strings. You cannot even go directly to the host and port (i.e. going to `127.0.0.1:8558` will give you error `The requested resource could not be found.`). You have to go _directly_ to different folders. For example:  
 * `http://127.0.0.1:8558/cluster/members` - shows the cluster members.  
 * To get a _specific_ node you have to write out its entire Akka address a la: `http://127.0.0.1:8558/cluster/members/akka://VandelayIndustries@127.0.0.1:25521`  
   * This also works on shards: `http://127.0.0.1:8558/shards/akka://VandelayIndustries@127.0.0.1:25521`  
* To set the status of a member to `Down` you can use `curl` - type this in a terminal:  
```
curl -X PUT -H 'Content-Type: multipart/form-data' -F operation=down http://127.0.0.1:8558/cluster/members/akka://VandelayIndustries@127.0.0.1:25521
```  
 * This is telling that we are sending form data (`Content-Type: multipart/form-data`) and that form data is `operation=down`.  
 * Note that if a node is unreachable, you cannot actually terminate it like this - this only works if you can confirm the node is actually not running.  
   * `Down` is for the _cluster_, but _terminated_ implies physically shutting down the application. If you send commands to the cluster to `Down` the member, it cant reach it so it cannot possibly contact it to terminate - you must do this yourself!  

Additional Features of Akka Management:  
* Akka Discovery - methods for locating and discovering services (Kubernetes, etc).  
* Akka Cluster Bootstrap - Automated seed node discovery using Akka Discovery.  
* Health Check Endpoints - Provides facilities for readiness and liveness checks.  

Akka Communication
* Akka cluster state is communicated using the Gossip Protocol - this means that the cluster nodes periodically sends their view of the cluster state to a random node.  
 * Things like:  
   * Status of each member.  
   * If the member has seen the provided cluster state (boolean).  
* There are different gossiping messages - each gossip message has its own unique ID.  This means each message tracks - and updates - the members who have seen the message.  
* Gossiping continues indefinitely.  
* Eventually all nodes will <font color="purple">converge</font> _in a stable cluster_, which means they all see the same state.  
 * This heavily depends on if _all_ other nodes have seen the specific gossip ID - this only happens when its been to all other available nodes and then returns back to the original node.  
 * Each node makes its own decision on if convergence has been obtained.  
 * Each node will have the same sorted set of nodes after convergence.  
 * The first node in the (sorted) list becomes the leader and will perform leader duties.  
   * Things like member state changes, etc.  
   * The leader may be different after each convergence _if_ cluster membership changed.  
* Member lifecycle
 * Note - a node can enter _any single state_ once and only once.  
 * A node begins in the `Joining` state.
 * Once convergence is reached, the node is changed to `Up`.  
 * The node enters the `Leaving` state when it wants to leave.  
   * The leader changes this to `Exiting` once convergence is reached.  
   * Once convergence is reached on the node `Exiting`, the leader sets it to `Removed`.  This hostname / port / UID can no longer join.  
 * There is also a variable that tracks the `Reachable` and `Unreachable` states, which is independent from the above states.  
   * A member can recover - that is, it can be `Unreachable` but becomes `Reachable` again.  
   * After a set period of time, the leader will mark it `Down` - which will then move to the `Removed` state.  
     * Unline `Reachable` and `Unreachable`, `Down` does indeed affect the normal states.  
   
Cluster Failure
* Each member in a cluster is monitored by up to 5 other members.  
* Heartbeats are used betweeen the members to verify communication.  
* Each member has a configurable Failure Detector.  
* A single failed heartbeat does not necessitate a member turning `Unreachable` - a _history_ of heartbeats determines that.  
* If one member determines another to be `Unreachable` it is broadcasted using the Gossip Protocol to all other members.  
* The member remains `Unreachable` until _all_ monitoring nodes detect it as `Reachable` again.  
 * Members will try to reach it until it is `Downed`.  
* The root cause of an `Unreachable` should _always_ be determined before adjusting the Failure Detector settings.  
 * Do not simply dismiss it as a network failure, as it could also be that the application crashed.  
 * Garbage collection can even caluse a node to become unreachable.  
* While a node is considered to be `Unreachable`, the _entire cluster_ cannot reach convergence.  
 * When one node is `Unreachable`, leader actions are not possible.  
 * `Unreachable` members _must_ become `Reachable` _or_ `Down` before new members can be _fully_ added.  
   * New members can join, but they are marked in state `WeaklyUp` - they can be used by applications but are not counted in important consistency decisions.
	 * `WeaklyUp` members _cannot_ host Cluster Shards or Cluster Singletons.  
   * When convergence is reached, `WeaklyUp` members are promoted to `Up`.  
* A <font color="purple">network partition</font> is a special type of failure - its when there is a major network interruption and the cluster splits into two (or more) independent clusters.  This is not good, as the clusters do not know of the other cluster's existence.  

Failure Recovery
* Usually, a re-start of the application will fix the problem.  
 * This will generate a new cluster UID for the application, and the member can re-join with this new UID.  
* A <font color="purple">network partition</font> cannot be automatically handled.  To handle it:  
 * Decide which partition to keep.  
 * Take down themembers that are in the partitioned cluster(s) you do not want to keep, but do not re-start until all members from the cluster that will not be kept are down.  
   * Old members _must_ be `Terminated` and then `Downed`.  
 * Restart the downed nodes.
   * To restart, see the section above (its a curl command).  
* <font color="purple">Split Brain</font> is a particular problem for cluster sharding.  
 * When downing an `Unreachable` member its important that the member be terminated first, otherwise this can happen.  
   * `Down` is for the _cluster_, but _terminated_ implies physically shutting down the application. If you send commands to the cluster to `Down` the member, it cant reach it so it cannot possibly contact it to terminate - you must do this yourself!  
 * <font color="purple">Split Brain</font> is when there is a <font color="blue">network partition</font> and an unreachable member continues to perform an operation; members that are not unreachable assume the cluster shards are finished and start the same oepration, so now there are two shards performing the same work.  
   * Unless you intervene, this can last indefinitely.  
 * Single Brain and data consistency
   * Cluster sharding / singleton requires single actor instances to be running in the cluster; since the split brain will cause multiple clusters, there will be two independent actor instances.  
   
Split Brain Resolver
* Static Quorum  
 * The `static-quorum` strategy uses a fixed size quorum of nodes (it is recommended that the quorum be over 50% of all nodes).  
 * If there is a potential split-brain issue, _all_ nodes evaluate their cluster.  
 * Any node that is unreachable will be `Downed` if and only if there is quorum.  
 * If the nodes do not have a quorum, they will terminate themselves.  
* Keep Majority  
 * The `keep-majority` strategy is very similar to `static-quorum`, but instead of using a pre-determined quorum, a majority is determined automatically.  
 * Every time a node enters the cluster, the cluster count increases; when a node _gracefully_ exits the cluster, the counter is decreased.  
 * if there are unreachable nodes, the cluster uses the counter to determine if it has the majority of nodes; if so, it `Downs` the unreachable nodes; if it does not have a majority, the nodes terminate themselves.  
* Keep Oldest  
 * The `keep-oldest` strategy does exactly what it sounds - connection to the oldest node means the node keeps itself alive.  
 * Any node that loses communication with the oldest node is `Downed`.  
 * If a node cannot contact the oldest node, it terminates itself.  
 * If the oldest node crashes, the _entire_ cluster will terminate.  
   * There is a setting to modify this - if this is set and the _only_ node that loses connectivity from the cluster is the oldest node, the other nodes `Down` the oldest node and the second to oldest node becomes the oldest node.  
* Keep Referee
 * `keep-referee` is alot like `keep-oldest` - except you pick a _specific_ node as the 'referee'.  
 * The referee is static and set by you.  
 * Any node that loses communication with the referee is `Downed`.  
 * If a node cannot contact the referee, it terminates itself.   
 * If the referee crashes, the entire cluster terminates.  
* Down All
 * `down-all` simply terminates the _entire_ cluster if one node becomes unreachable.  
 * This relies on outside tools to restart the cluster.  
* Lease Majority
 * `lease-majority` uses a distributed lock (lease) located remotely.  
 * Each part of the broken cluster will try to get the lock.  
 * If the cluster does not get the lease, it terminates.  
 * There is a delay for the cluster with the minority of nodes so the chances of the majority being kept is higher.  
 * This is currently (as of Akka 2.6) only available for Kubernetes deployments.  


**Cluster Sharding**  

Overview
* <font color="green">Akka Cluster Sharding</font> distributes actors across a cluster of machines.  
* The actors provide a strong consistency guarantee.  
* Adheres to the <font color="purple">Reactive Principles</font>:  
 * Responsive  
 * Resilient  
 * Elastic  
 * Message Driven  
* <font color="green">Sharding</font> (or <font color="green">Partitioning</font>) is the concept of distributing data across multiple servers (as opposed to one server) in order to decrease the load on a particular server.  
 * This is accomplished with a <font color="purple">router</font>, which routes the request to the appropriate server.  
 
External Caches  
* <font color="purple">Caching</font> is used to reduce the load on a database by storing data in an external / remote in-memory cache.  
 * Write operations will update _both_ the cache _and_ the database.  
 * Read operations _fully_ come from the cache - this means the database is (usually) not contacted for reads!  
 
* External shared caches (like Memcached) can be used to store data accessed by distributed systems.  
* This means there is a single point of contact, and sharding the cache can help, but how do you ensure the cache and database are updated transactionally?  
 * Consistency requires a single source of truth - consistency is lost if there are multiple instances with their own cache.  
 
Akka Cluster Sharding  
* <font color="green">Akka Cluster Sharding</font> distributes _actors_ across a cluster.  
 * Each actor acts as a consistency boundary for its own data.  
* Components of Cluster Sharding:  
 * Entities  
 * Shards  
 * Shard Regions  
 * Shard Coordinator  
* <font color="green">Entities</font> are what actors are called in a cluster.  
 * They have a unique entity ID across the entire cluster - even separate entities, apparently.  
   * These appear to be the name given to it - i.e. `context().self().path().name`  
   * You should make _SURE_ the entity / actor names are unique in the cluster!  
 * This allows for strong Consistency.  
 * Entities are in collections in shards.  
 * Shards are divided into Shard Regions - a Shard Region has many shards.  
 * For each type of entity, there is one shard region per node / JVM.  
* The <font color="green">Shard Coordinator</font> manages shards.  
 * It is responsible for ensuring the intended messges get sent to a specific entity.  
 **NOTE**: the <font color="green">Shard Coordinator</font> entirely relies on <font color="green">Akka Distributed Data</font> or <font color="green">Akka Persistence</font>.  
 
The Message  
* Messages need an <font color="green">Extractor Function</font> processes incoming messages, which separates it into  
 * An entity ID.  
 * A message to be forwarded.  
* An envelope object is usually made / used, which contains the entity ID and the message.  
* An envelope is unnecessary if you can extract the entity ID from the message.  
 * i.e., there is an entity ID string in the message.  

More on Shards  
* Shards have a unique shard ID.  
* Shard Ids are unique, but multiple entities can have the same shard ID.  
* Its the best practice to have roughly 10 shards per node, so a typical ID spread is 0 - 9
 * Because of this setup, a typical way to determine an Entity's shard ID is: Math.abs(EntityID.hashCode % totalShards)
   * Do _not_ use the absolute value _before_ the modulo, otherwise there can be a bug if the hashcode is the minimum value!  
 * Too many shards means there is a cost in finding the shards.  
 * Too few shards reduces the abolity to scale up or down.  
 * A good sharding strategy lends to a random and evenly distribution of entities.  
 * A bad strategy leads to hotspots with an uneven workload.  
   * Hotspots are usually the result of an uneven workload _or_ when busy entities are clustered in the same shard.  
   
More on Entities
* Entity IDs  
 * These identify each Entity - its the same as an actor name.  
 * They MUST be unique in the entire cluster!  
 * This is usually tied to the domain concept you are modelling.   
   * An employee ID, customer ID, etc.  
 