# Akka: Clustering and Remote Actors

# Clustering Basics

> For more on how clustering works, see the [Akka documents](http://doc.akka.io/docs/akka/snapshot/scala/cluster-usage.html).  

Akka can utilize [clustering](learn_to_code/computer_science_concepts?id=clustering) in order to help scale the application and mitigate [node](learn_to_code/computer_science_concepts?id=clustering) failures. 

## CAP Theorem  



## Node Monitoring 

Akka monitors all nodes in its cluster; however, no sigle node monitors all other nodes (as that becomes impractical once there are dozens - or hundreds - of [nodes](learn_to_code/computer_science_concepts?id=clustering)). Usually, one [node](learn_to_code/computer_science_concepts?id=clustering) will monitor a few of its immediate neighbors via a heartbeat mechanism (the default number of nodes a single node will monitor itself is 5 [nodes](learn_to_code/computer_science_concepts?id=clustering)). If a node cannot be reached, it is considered unavailable to all nodes in the cluster.

This method of node monitoring is 'eventually consistent' and is known as <font color="purple">gossip protocol</font> or <font color="purple">epidemic protocol</font>.  

62/85
161/184

src/main/resources/application.conf