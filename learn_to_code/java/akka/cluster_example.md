# Akka Cluster Examples Examples

# Basic Cluster Sharding Example 

This is a basic Akka example that uses cluster sharding. The basic premise of this project is: there are some cluster hosts that throw fruit and some cluster hosts that catch fruit. 

It uses [Maven](learn_to_code/java/maven) to build the project; in addition, it uses [protobuf](learn_to_code/java/protobuf), so please have that installed.

!> This entire project assumes the Java package is `com.wagenseller`; feel free to change, but make sure to change it everywhere.  


## Basic Sharding Example - pom.xml 

This is the Maven [pom](learn_to_code/java/akka/akka_basic_examples?id=pomxml) file for the project:  
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.wagenseller</groupId>
  <artifactId>clusterShard</artifactId>
  <packaging>jar</packaging>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <mainClass>com.wagenseller.ClusterShardingExample</mainClass>

    <!-- dependency versions -->
    <akka.version>2.6.10</akka.version>
    <scala.version>2.12</scala.version>
    <protobuf.executable>/usr/local/bin/protoc</protobuf.executable>
    <protobuf.version>3.10.0</protobuf.version>
  </properties>

  <version>1.0.0-SNAPSHOT</version>
  <name>clusterShard</name>
  <url>http://maven.apache.org</url>

  <build>
    <plugins>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.0</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
        </configuration>
      </plugin>

      <!-- necessary to include all dependency jars in the jar file -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.0.0</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>

              <!-- This transformer is _required_ - otherwise applications.conf will _not_ be filled out with defaults and you will have to define everything!
              see: https://stackoverflow.com/questions/31011243/no-configuration-setting-found-for-key-akka-version
              -->
              <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                  <resource>reference.conf</resource>
                </transformer>
              </transformers>
            </configuration>
          </execution>
        </executions>

      </plugin>
      <plugin>
        <artifactId>maven-jar-plugin</artifactId>
        <version>3.0.2</version>
        <configuration>
          <archive>
            <manifest>
              <addClasspath>true</addClasspath>
              <classpathPrefix>lib/</classpathPrefix>
              <mainClass>${mainClass}</mainClass>
            </manifest>
          </archive>
        </configuration>
      </plugin>


      <!-- necessary for protobuf (i.e. the serialized messages required by Akka) -->
      <plugin>
        <groupId>org.xolstice.maven.plugins</groupId>
        <artifactId>protobuf-maven-plugin</artifactId>
        <version>0.5.1</version>
        <configuration>
          <clearOutputDirectory>true</clearOutputDirectory>
          <outputDirectory>target/generated-sources/java</outputDirectory>
          <protocExecutable>${protobuf.executable}</protocExecutable>
        </configuration>
        <executions>
          <execution>
            <phase>generate-sources</phase>
            <goals>
              <goal>compile</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>

  </build>
  <dependencies>

    <!-- required Akka packages-->
    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-cluster-sharding_${scala.version}</artifactId>
      <version>${akka.version}</version>
    </dependency>
    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-cluster-typed_${scala.version}</artifactId>
      <version>${akka.version}</version>
    </dependency>

    <!-- Needed for the serialized protobuf messages - Akka requires serialized classes, and I use protobuf for this-->
    <dependency>
      <groupId>com.google.protobuf</groupId>
      <artifactId>protobuf-java</artifactId>
      <version>${protobuf.version}</version>
    </dependency>

    <!-- Needed for the log - simply using the one from Akka above does _not_ work, you need both-->
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.2.3</version>
      <scope>runtime</scope>
    </dependency>
  </dependencies>
</project>
```  
* This assumes the [protobuf](learn_to_code/java/protobuf) binary file is located at `/usr/local/bin/protoc`; if this is not where it is located, please point the variable `protobuf.executable` to the correct binary!  


## Basic Sharding Example - application.conf

This is the [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf) file; it should go in your [resources](learn_to_code/java/maven?id=resources-in-maven) folder.  
``` 
akka {
  log-config-on-start : off

  # Loggers to register at boot time (akka.event.Logging$DefaultLogger logs to STDOUT)
  loggers : ["akka.event.slf4j.Slf4jLogger"]

  loglevel : "DEBUG"
  
  stdout-loglevel : "DEBUG"

  actor {
    provider : cluster
  }
  
  remote {
    artery {
      transport : tcp
      canonical {
        hostname : "127.0.0.1"
        port : 25520
      }

    }
  }
  
  cluster {
    seed-nodes : ${myseeds.cluster.nodes}

    # downing-provider-class : "akka.cluster.sbr.SplitBrainResolverProvider"

    split-brain-resolver {
      active-strategy : keep-majority
    }

    # This sets this host's 'roles'. Its possible that there are many nodes in a clustered Akka system; its also possible you only want a few of them performing specific clustered tasks. This is
    # where roles come in. A 'role' can be a job, a task, etc that you would like this specific host to perform; then, you can specify a role for the specific sharded cluster as well. If the specific
    # sharded cluster's role matches an item in the host's list, the host will be a part of that cluster.
    # Note that setting the roles are not necessary UNLESS you want to sue a cluster shard and at least one of the following are true:
    # * You want _some_ hosts to be a simple cluster proxy (i.e. will not host any entities).
    # * You want to have specific hosts have specific cluster tasks / jobs (so not all hosts will act the same)
    #
    # If you simply want all hosts to act the same, roes are not necessary.
    #
    # Finally, note that akka.cluster.roles _can_ be overwritten (and usually are, which will enable different hosts to have different jobs.
    #
    # If uncommented, the line below means this host will have two sharded cluster 'jobs': "FruitCatcher" and "FruitThrower". Remember, _this can be overwritten_.
    # roles : ["FruitCatcher","FruitThrower"]
    #
    # However, we will default to just the FruitCatcher:
    roles : ["FruitCatcher"]

    sharding {
      # Setting the role is not necessary UNLESS a cluster proxy is used - if it is, then this must be set AND akka.cluster.roles must ALSO be set
      # Note that this does NOT have to be set here - it can also be set when starting the individual cluster shard in the settings (i.e.
      # ClusterShardingSettings.create(actorSystem).withRole("FruitCatcher") ). Setting this through the settings is probably desirable, because you can set different roles
      # for different cluster shard types (as opposed to a blanket setting here). That said, ALL roles used MUST be defined in the list akka.cluster.roles.

      # The default role for _all_ cluster shards. Remember that all cluster nodes require a ClusterShardingSettings object, and its in this object where the _specific_ cluster shard's role can
      # be set. If you have multiple cluster shards in your project, you will almost certainly overwrite this role.
      #
      # The default role for all shards in this project is "FruitCatcher".
      role : "FruitCatcher"

      # For rebalancing, its important to note that rebalancing happens on the _shard_ level and NOT the _entity_ level.

      # The rebalance-interval. There is a default (which is used here), but this is how it can be set. Note its not good to set this to 0, otherwise its possible that shard regions will _not_ be
      # launched when a new node joins the cluster in addition to the shards _not_ being re-balanced (so messages to them will just go to a black hole).
      # rebalance-interval : 10s

      # max-simultaneous-rebalance : 3

      # IF 'remember-entities' si set t oon, this will automatically kill the entity if it has not received any messages for this amount of time. For example. 5s = 5 seconds, 1m = 1 minute.
      # 'off' means you have to manually kill the entity with a PoisonPill.
      passivate-idle-entity-after : 3s

      # If this is on, the entities will persist - even if you give them a PoisonPill!
      remember-entities : off
    }

    min-nr-of-members : 1
  }
}

additional {
    max-shards: 3
    time-to-live: 1000
    port-of-entry : 25520
    print-actor-start-and-stops: false
}

myseeds {
    cluster {
      nodes : [
      "akka://CLUSTEREXAMPLE@127.0.0.1:25520"
      ]
    }
}

```  

## Basic Sharding Example - logback.xml

This project uses [logback](learn_to_code/java/logback); this is the setup file [logback.xml](learn_to_code/java/logback?id=logbackxml); it should go in your [resources](learn_to_code/java/maven?id=resources-in-maven) folder.  

```
<configuration scan="false" debug="true">
    <conversionRule conversionWord="traceToken" converterClass="kamon.trace.logging.LogbackTraceTokenConverter"/>
    <statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />
    <property file="/etc/log.properties" />

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS,UTC} %highlight(%level) [%thread] %cyan(%logger{36}) %magenta(%X{akkaSource}) %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="com.wagenseller" level="${LOGLEVEL_WAGENSELLER:-DEBUG}" additivity="false">
        <appender-ref ref="STDOUT"/>
    </logger>

    <!-- This one may have some good info on the debug level but it spams - you should turn this on debug and see how it works. -->
    <logger name="akka.actor.LocalActorRefProvider(akka://CLUSTEREXAMPLE)" level="${LOGLEVEL_AKKA_LOCAL_ACTOR_CLUSTEREXAMPLE:-INFO}" additivity="false" >
        <appender-ref ref="STDOUT" />
    </logger>

    <logger name="akka.actor" level="${LOGLEVEL_AKKA_ACTOR:-INFO}" additivity="false" >
        <appender-ref ref="STDOUT" />
    </logger>
    <logger name="akka.cluster" level="${LOGLEVEL_AKKA_CLUSTER:-INFO}" additivity="false" >
        <appender-ref ref="STDOUT" />
    </logger>
    <logger name="akka.io" level="${LOGLEVEL_AKKA_IO:-INFO}" additivity="false" >
        <appender-ref ref="STDOUT" />
    </logger>
    <logger name="akka.event.EventStream" level="${LOGLEVEL_AKKA_EVENTSTREAM:-INFO}" additivity="false" >
        <appender-ref ref="STDOUT" />
    </logger>
    <logger name="akka.remote" level="${LOGLEVEL_AKKA_REMOTE:-INFO}" additivity="false" >
        <appender-ref ref="STDOUT" />
    </logger>
    <logger name="akka.serialization" level="${LOGLEVEL_AKKA_SERIALIZATION:-INFO}" additivity="false" >
        <appender-ref ref="STDOUT" />
    </logger>
    <logger name="akka.stream" level="${LOGLEVEL_AKKA_STREAM:-INFO}" additivity="false" >
        <appender-ref ref="STDOUT" />
    </logger>


    <root level="DEBUG">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```  

## Basic Sharding Example - SerializedClasses.proto  


This file (<font color="green">SerializedClasses.proto</font>) is the [protobuf](learn_to_code/java/protobuf) class file and should go in the `src/main/proto/` directory. Running `mvn clean install` in the main directory of the project (where the pom file resides) will generate the necessary classes defined in this file.  

```
syntax = "proto3";
package wagenseller.msg;

option java_package = "com.wagenseller.msg.fruit";
option java_outer_classname = "SerializedClasses";
option optimize_for = CODE_SIZE;

import "google/protobuf/any.proto";

message FruitEnvelope {
    int32 fruitIdentifier = 1;
    google.protobuf.Any message = 2;
}

message Apple {
    int32 appleNumber = 1;
}

message Orange {
    int32 orangeNumber = 1;
}
```  

## ClusterShardingExample.java  

This file (<font color="green">ClusterShardingExample.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  
```
package com.wagenseller;

import akka.actor.ActorRef;
import akka.actor.ActorSystem;
import akka.cluster.sharding.ClusterSharding;
import akka.cluster.sharding.ClusterShardingSettings;
import com.google.protobuf.Any;
import com.typesafe.config.Config;
import com.typesafe.config.ConfigFactory;
import com.wagenseller.msg.fruit.SerializedClasses;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.List;
import java.util.Optional;
import java.util.concurrent.ThreadLocalRandom;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * This project is an example of how Akka cluster sharding works; this project has the ability to be a producer (which throws apples and oranges), a consumer (which catches apples and oranges), or
 * both.
 *
 *
 * This is the main java file that holds the run function. You can run as many catchers and throwers as you want, but you should have at least one catcher (cluster-roles="FruitCatcher") and one
 * thrower (cluster-roles="FruitThrower"); by default the main catcher (NOT a proxy) is expected to use port 25520, but you can change that if you wish.
 *
 * To run:
 * Catcher: java -Dadditional.time-to-live=1000 -Dakka.remote.artery.canonical.port=25520 -Dcluster-roles="FruitCatcher" -cp clusterShard-1.0.0-SNAPSHOT.jar com.wagenseller.ClusterShardingExample
 * Thrower: java -Dadditional.time-to-live=1000 -Dakka.remote.artery.canonical.port=25522 -Dcluster-roles="FruitThrower" -Dadditional.port-of-entry=25521 -cp clusterShard-1.0.0-SNAPSHOT.jar com.wagenseller.ClusterShardingExample
 *
 *
 * Observations:
 * 1. Setting the max shards means there are that many _supervisors_ (or something like it) created - for example, if this is set to 3 here, there will be these actors created:
 *      * akka://CLUSTEREXAMPLE/system/sharding/shardedAppleCatcher/0/XXX
 *      * akka://CLUSTEREXAMPLE/system/sharding/shardedAppleCatcher/1/XXX
 *      * akka://CLUSTEREXAMPLE/system/sharding/shardedAppleCatcher/2/XXX
 *      NOTE the XXX - see below
 * 2. EVERY cluster member should probably have the same sharded cluster actors - either an active cluster shard _or_ a proxy. If one does not, its possible they will ALL fail with a message like the below. Apparently the check for the shard will
 *      fail if all actors do not have the _same_ setup and _no_ messages will be processed!
 *          The ShardCoordinator was unable to update a distributed state within 'updating-state-timeout': 5000 millis (retrying). Perhaps the ShardRegion has not started on all active nodes yet? event=ShardHomeAllocated(2,Actor[akka://CLUSTEREXAMPLE/system/sharding/shardedParent#-341710716])
 * 3. A child actor is created _for every unique request received_ (see the XXX in the point above); these are known as entities. The XXX name is actually returned by the entityId() method you have
 *      to override.
 * 4. The shards seem to be distributed evenly as possible; in addition, they are re-distributed if a node joins or leaves.
 * 5. The maxShards have no control over the XXX - they seem to come and go as they please, based on the entityId() method.
 * 6. Protobuf does not have a great answer to hashing the unique entity ID that is needed - so what can be done is a static entityIdToHash() can be written that simply turns
 *      any ID into a hash via 'return Objects.hash(ID);'; this can be used in the required method shardId() (btw, dont mess this up, BOTH the 'if' amd 'else if' have to return
 *      the SAME value for the same ID, so using the 'return Objects.hash(ID);' trick as I do here is helpful.
 * 7. The same entityID goes to the same shard.
 * 8. Re-balancing does NOT happen on the entity level - it happens on the shard level. An individual entity will not be rebalanced - if anything is rebalanced, it will
 *      be an entire shard.
 * 9. If using a cluster shard proxy, Akka will tack the word 'Proxy' on to the end of the name. You can still access the sharded cluster via ClusterSharding.get().shardRegion() like so:
 *
 *     ClusterSharding.get(context().system()).shardRegion("SomeShardedClusterName")
 *
 *     You can do that without having to tack the word 'Proxy' on at the end; however, if you try to _directly_ access the cluster shard on a host using a proxy like so, it will fail:
 *
 *     context().system().actorSelection("akka://CLUSTEREXAMPLE/system/sharding/SomeShardedClusterName").tell(new Identify(1), self());
 *
 *     You will have to tack the word 'Proxy' onto the end like so:
 *
 *     context().system().actorSelection("akka://CLUSTEREXAMPLE/system/sharding/SomeShardedClusterNameProxy").tell(new Identify(1), self());
 *
 * 10. A good max shard count is ten times the number of hosts.
 *
 *
 *
 *
 * If 'remember-entities" is on in application.conf:
 * 1. A directory is made (in the same directory as the jar) and it will be called something like 'ddata-CLUSTERNAME-replicator-PORT' (for this
 *      example, its called 'ddata-CLUSTEREXAMPLE-replicator-25520'). This directory seems to persist, _even after a shutdown_, and will _revive_ actors in here.
 * 2. Old entities (the XXX above) that were closed will eventually start again on their own. This may be because clustering is actually supposed to support data that is unique,
 *    (things like a customerID) and that customerID should always exist - so if it is closed, its resurrected as its expected to be operational. Because this is the case, if
 *    you need clustering simply for load balancing and they should not per persistent, do _not_ set remember-entities to 'on'!
 * 3. Child actors will persist _even after the jar shuts down  and restarts_.
 * 4. There is only one way to kill an entity if 'remember-entities" is on: the entry _itself_ MUST send the message 'ShardRegion.Passivate(PoisonPill.getInstance())' to its parent like so:
 *
 *    getContext().getParent().tell(new ShardRegion.Passivate(PoisonPill.getInstance()), getSelf());
 *
 *
 * Links:
 * Configuration - https://doc.akka.io/docs/akka/current/typed/cluster-sharding.html#configuration
 */

public class ClusterShardingExample {

    private static final Logger logger = LoggerFactory.getLogger(ClusterShardingExample.class);
    private ActorSystem actorSystem;

    private void runCode(String[] args) {

        // load the command line arguments
        loadConfigOverrides(args);

        /*
        The below sets up the Akka actor system's configuration.

        Unfortunately, lists do not work well with importing from arguments - so we have to get the list (as a String) and THEN parse it with Akka's 'ConfigFactory.parseString()' method
        */
        Config clusteringConfig = null;
        String clusterRoles = System.getProperty("cluster-roles");
        if ((clusterRoles == null) || (clusterRoles.equals(""))) {
            // If "cluster-roles" did NOT exist in the command line
            logger.info("Cluster rules not overwritten.");
            clusteringConfig = ConfigFactory.load().resolve();
        } else {
            // If "cluster-roles" existed in the command line, we gotta do this to properly load into Akka's config
            logger.info("Cluster rules overwritten: [{}]", clusterRoles);
            clusteringConfig = ConfigFactory.parseString("akka.cluster.roles=[" + clusterRoles +  "]").withFallback(ConfigFactory.load()).resolve();
        }


        // start the actor system with an actor system name CLUSTEREXAMPLE
        try {
            actorSystem = ActorSystem.create("CLUSTEREXAMPLE", clusteringConfig);
        } catch (Exception e) {
            logger.error("error::::::::::::::::::::::::::::::: [{}]", e.getMessage(), e);
        }

        List hostRoles = actorSystem.settings().config().getStringList("akka.cluster.roles");

        logger.info(" This host's roles: {}", hostRoles.toString());


        if (hostRoles.contains("FruitCatcher")) {
            // If the role of 'FruitCatcher" is assigned to this host, launch the fruit catching cluster
            launchFruitClusterActor();

        } else {
            /*
               If the role of 'FruitCatcher" is NOT assigned to this host, we still want to set up a proxy. This proxy will not actually service any shard / entity. The only purpose this will
               serve is it will allow this host to directly send a message to the cluster in order to communicate with the cluster - otherwise, we could only send messages to the cluster via a
               direct remote connection, and that is undesirable (as we would have to specify an IP / port in a direct remote connection, and that is not always easily available in a cluster).
               Note: the name MUST be the same as it is on the ACTUAL cluster shard!

               The setup of a proxy can be done in one of two ways:
                 *  Set up the cluster as you normally would, but do NOT assign this host the role of "FruitCatcher".
                    * If it hits this point in the if / else above, this host has NOT been assigned the role of "FruitCatcher".
                    * If the host has NOT been assigned the role of "FruitCatcher" but the role of the shard is assigned 'FruitCatcher', launching the cluster normally / as done in the 'if' above
                      _will launch the cluster as a proxy for this host_. If the role of the sharded cluster is not in the host's list of roles, the cluster will default to a proxy.
                 * Deliberately launch a proxy cluster host

               I will do it both ways using a random to choose. I do this just to showcase how both are done.
            */
            int randomProxyMethod = ThreadLocalRandom.current().nextInt(0, 2);

            if (randomProxyMethod == 0) {
                // if randomProxyMethod is 0 we will just define the cluster as normal - note, however, that since this host has NOT been assigned the role of "FruitCatcher" it will be forced
                // as a proxy
                ActorRef shardedFruitCatcher = launchFruitClusterActor();

                // This prints out the cluster shard name - note Akka FORCED the word 'Proxy' at the end of the name. Akka does this for Proxies! (Dont worry though, you can still find the proxy
                // using the name in ShardedFruitCatcher.CLUSTER_NAME ).
                logger.warn("A cluster shard for the ShardedFruitCatcher is not used on this host - proxy cluster [{}] set up via the mismatch role method.", shardedFruitCatcher.path());
            } else {
                // This will deliberately set up the cluster as a proxy. Again, both method shave the same result, but I wanted to showcase both

                int maxShards = actorSystem.settings().config().getInt("additional.max-shards");

                // setup a cluster proxy.
                // I am guessing, but I am assuming the maxShards MUST be the same between the actual cluster and the proxies!
                ActorRef shardedFruitCatcher = ClusterSharding.get(actorSystem).startProxy(ShardedFruitCatcher.CLUSTER_NAME, Optional.empty(), ShardedFruitCatcher.messageExtractor(maxShards));

                // This prints out the cluster shard name - note Akka FORCED the word 'Proxy' at the end of the name. Akka does this for Proxies! (Dont worry though, you can still find the proxy
                // using the name in ShardedFruitCatcher.CLUSTER_NAME ).
                logger.warn("A cluster shard for the ShardedFruitCatcher is not used on this host - proxy cluster [{}] set up via the deliberate method.", shardedFruitCatcher.path());
            }

        }

        if (hostRoles.contains("FruitThrower")) {
            // If this host is designed to be throwing fruit

            // This is the port of a host that has the role of 'FruitCatcher' (or a proxy of it). In practice, ALL of the members of this actorsystem will be running the actual cluster OR a proxy,
            // so you could point this at any cluster member, but its best to set this to a host that is actually running a cluster shard and not a proxy
            int destinationPort= actorSystem.settings().config().getInt("additional.port-of-entry");

            // This actor throws the fruit
            ActorRef actorThrowingApples = actorSystem.actorOf(FruitThrowerActor.props(destinationPort), "appleThrowingActor");

        }

        // regardless if this is designed to throw fruit or not, throw two pieces of fruit
        sendTwoTestMessages();

    }

    private ActorRef launchFruitClusterActor() {

        int port = actorSystem.settings().config().getInt("akka.remote.artery.canonical.port");
        int maxShards = actorSystem.settings().config().getInt("additional.max-shards");
        int timeToDie= actorSystem.settings().config().getInt("additional.time-to-live");

        // This is the settings for the sharded cluster.  Using ClusterShardingSettings.create(actorSystem) is basically the default, but you can override settings
        // (which are initially set in application.conf) here as well (for example, we override the required role the host must have to launch ShardedFruitCatcher entities
        // here - we do not have to do this as this is
        ClusterShardingSettings shardSettings = ClusterShardingSettings.create(actorSystem).withRole("FruitCatcher");

        // Launch the two local actors that will 'catch' the fruit.
        ActorRef localAppleCatcher = actorSystem.actorOf(LocalAppleCatcherActor.props(port), "LocalAppleCatcherActor");
        ActorRef localOrangeCatcher = actorSystem.actorOf(LocalOrangeCatcherActor.props(port), "LocalOrangeCatcherActor");

        // Actually launch the cluster, It doesnt matter that we do not do anything with this ActorRef - once launched, its in memory until it gts a PoisonPill or is otherwise shut down!
        ActorRef shardedFruitCatcher = ClusterSharding.get(actorSystem).start(
                ShardedFruitCatcher.CLUSTER_NAME,
                ShardedFruitCatcher.props(localAppleCatcher, localOrangeCatcher, port, timeToDie),
                shardSettings,
                ShardedFruitCatcher.messageExtractor(maxShards)
        );

        return shardedFruitCatcher;
    }

    /**
     * This simply sends two test messages to the cluster shard.
     */
    private void sendTwoTestMessages() {


        // Build an apple and an orange message (and their envelopes) for a test
        SerializedClasses.Apple firstApple = SerializedClasses.Apple.newBuilder().setAppleNumber(10000).build();
        SerializedClasses.Orange firstOrange = SerializedClasses.Orange.newBuilder().setOrangeNumber(10001).build();

        SerializedClasses.FruitEnvelope appleEnvelope = SerializedClasses.FruitEnvelope.newBuilder()
                .setMessage(Any.pack(firstApple)).setFruitIdentifier(firstApple.getAppleNumber()).build();

        SerializedClasses.FruitEnvelope orangeEnvelope = SerializedClasses.FruitEnvelope.newBuilder()
                .setMessage(Any.pack(firstOrange)).setFruitIdentifier(firstOrange.getOrangeNumber()).build();

        try {
            Thread.sleep(10000);
        } catch (java.lang.InterruptedException e) {

        }

        // This will show how you can send an message from within the actorSystem to the cluster without an actorRef. This will work so long as the name is known.
        ActorRef clusterShardingSystem = ClusterSharding.get(actorSystem).shardRegion(ShardedFruitCatcher.CLUSTER_NAME);
        clusterShardingSystem.tell(appleEnvelope, null);

        clusterShardingSystem.tell(orangeEnvelope, null);

        logger.info("ClusterSharding path is [{}]", clusterShardingSystem.path());
    }

    /**
     * This overrides the system properties with arguments from the command line; this is helpful, as Akka _also_ draws from the System properties - so anything loaded into System.setProperty()
     * _also_ gets loaded into Akka (as if it were in application.conf).
     *
     * In addition, this uses a clever pattern matching schema to extract each parameter and its value using a regex.
     *
     * @param args
     */
    private static void loadConfigOverrides(String[] args) {
        String regex = "-D(\\S+)=(\\S+)";
        Pattern pattern = Pattern.compile(regex);

        for (String arg : args) {
            Matcher matcher = pattern.matcher(arg);

            while(matcher.find()) {
                String key = matcher.group(1);
                String value = matcher.group(2);
                logger.info("Config Override: "+key+" = "+value);
                System.setProperty(key, value);
            }
        }
    }


    public static void main(String[] args) {
        new ClusterShardingExample().runCode(args);
    }
}
```  

## FruitThrowerActor.java  

This file (<font color="green">FruitThrowerActor.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.actor.*;
import akka.cluster.sharding.ClusterSharding;
import com.google.protobuf.Any;
import com.wagenseller.msg.fruit.SerializedClasses;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.concurrent.ThreadLocalRandom;

/**
 * This actor acts as a local actor that is unaffiliated with any cluster. If the host is a thrower (cluster-roles="FruitThrower"), it will launch this actor, which will periodically throw fruit.
 *
 */

public class FruitThrowerActor extends AbstractActorWithTimers {

    private static final Logger logger = LoggerFactory.getLogger(FruitThrowerActor.class);

    private static int THROW_INTERVAL = 3000;
    private static int CHECK_REMOTE_CONNECTION_INTERVAL = 15000;
    private static int REMOTE_CONNECTION_TIMEOUT_INTERVAL = 1000;


    private static final class WindMyArm {}
    private static Object WIND_MY_ARM = "WIND_MY_ARM";

    private static final class CheckRemoteConnection {}
    private static Object CHECK_REMOTE_CONNECTION = "CHECK_REMOTE_CONNECTION";

    private static final class RemoteConnectionTimeout {}
    private static Object REMOTE_CONNECTION_TIMEOUT = "REMOTE_CONNECTION_TIMEOUT";


    private int remotePort;
    private int numFruitThrown = 0;

    /*
        There are two ways this can connect to the cluster shard coordinator - via a remote call OR through the local proxy cluster. Only one is needed, but I use both here to demonstrate
        how both can be used (as both are a bit different).

        The only downside to using the proxy is there is no real way to tell if the entire cluster is defunct _without_ some remote call checking on a specific actor's identity.

    */
    ActorRef remoteClusterShard;
    ActorRef localClusterProxyShardRegion;

    static public Props props(int remotePort) {
        return Props.create(FruitThrowerActor.class, () -> new FruitThrowerActor(remotePort));
    }

    public FruitThrowerActor(int remotePort) {
        this.remotePort = remotePort;
    }

    @Override
    public void preStart() {
        logger.info("FruitThrowerActor started! remote port [{}]", remotePort);

        // start the timer that will throw fruit
        getTimers().startTimerWithFixedDelay(WIND_MY_ARM, new WindMyArm(), java.time.Duration.ofMillis(THROW_INTERVAL));

        // This is the local sharded fruit cluster - it will either be a reference to the actual cluster OR a proxy
        localClusterProxyShardRegion = ClusterSharding.get(context().system()).shardRegion(ShardedFruitCatcher.CLUSTER_NAME);

        // Make the first check that queries the remote connection
        checkRemoteConnection();
        getTimers().startTimerWithFixedDelay(CHECK_REMOTE_CONNECTION, new CheckRemoteConnection(), java.time.Duration.ofMillis(CHECK_REMOTE_CONNECTION_INTERVAL));

    }

    public void checkRemoteConnection() {

        String remoteAddress = "akka://CLUSTEREXAMPLE@127.0.0.1:" + remotePort + "/system/sharding/" + ShardedFruitCatcher.CLUSTER_NAME;
        if((remoteClusterShard != null) && (remoteClusterShard.isTerminated() == false)) logger.info("Re-checking connection...");
        else logger.info("Initial remote request made to [{}]!", remoteAddress);
        // ask for the identity of the remote shardedParent cluster shard
        //NOTE this does NOT use /user/ in the actor path name - it uses /system/sharding/
        context().system().actorSelection(remoteAddress).tell(new Identify(1), self());

        // we also send an Identity request to the same remote address, but we tack a 'Proxy' on to the end of the actor name. Why? If the source is a proxy, Akka will tack the word 'Proxy' on to
        // the end of the actor name. If this is the case, requesting the Identity WITHOUT the word 'Proxy' tacked on at the end will fail.
        context().system().actorSelection(remoteAddress + "Proxy").tell(new Identify(2), self());

        // start the timer to check to see if the connection timed out; the first thing you must do is cancel the timer, IF its running
        getTimers().cancel(REMOTE_CONNECTION_TIMEOUT);
        getTimers().startSingleTimer(REMOTE_CONNECTION_TIMEOUT, new RemoteConnectionTimeout(), java.time.Duration.ofMillis(REMOTE_CONNECTION_TIMEOUT_INTERVAL));

    }

    public void throwFruit() {
        numFruitThrown++;

        /*
            There are two ways this can connect to the cluster shard coordinator - via a remote call OR through the local proxy cluster. Only one is needed, but I use both here to demonstrate
            how both can be used (as both are a bit different).

            The only downside to using the proxy is there is no real way to tell if the entire cluster is defunct _without_ some remote call checking on a specific actor's identity.
        */
        int randomRemoteMethod = ThreadLocalRandom.current().nextInt(0, 2);

        // Get a random fruit (either an apple or an orange)
        int randomFruit = ThreadLocalRandom.current().nextInt(0, 2);

        SerializedClasses.FruitEnvelope msg = null;

        if((remoteClusterShard != null) && (remoteClusterShard.isTerminated() == false)) {

            if (randomFruit == 0) {
                logger.info("FruitThrowerActor throwing apple #[{}] using remote method [{}]!", numFruitThrown, ((randomRemoteMethod == 0)?"direct remote":"cluster proxy"));

                SerializedClasses.Apple apple = SerializedClasses.Apple.newBuilder().setAppleNumber(numFruitThrown).build();
                msg = SerializedClasses.FruitEnvelope.newBuilder().setFruitIdentifier(numFruitThrown).setMessage(Any.pack(apple)).build();
            } else {
                logger.info("FruitThrowerActor throwing orange #[{}] using remote method [{}]!", numFruitThrown, ((randomRemoteMethod == 0)?"direct remote":"cluster proxy"));

                SerializedClasses.Orange orange = SerializedClasses.Orange.newBuilder().setOrangeNumber(numFruitThrown).build();
                msg = SerializedClasses.FruitEnvelope.newBuilder().setFruitIdentifier(numFruitThrown).setMessage(Any.pack(orange)).build();
            }

            // send the message, where the method depends on randomRemoteMethod
            if(randomRemoteMethod == 0) remoteClusterShard.tell(msg, self());
            else localClusterProxyShardRegion.tell(msg, self());

        } else {
            if (remoteClusterShard == null) logger.warn("Cannot throw fruit #[{}] - the Identify request has not returned!", numFruitThrown);
            else if (remoteClusterShard.isTerminated()) logger.warn("Cannot throw fruit #[{}] - the remoteClusterShard was terminated!", numFruitThrown);
            // The 'else if' will actually never work _if_ the remote system is shutdown, as apparently the actorRef is not notified

            // We do NOT want to attempt a re-connect here - if we do it could cause a re-connect storm, as EVERY failed attempt would then attempt a reconnect in addition to the
            // CHECK_REMOTE_CONNECTION timer. That could be uncontrollable. Its best to simply leave the re-connection checks up to the CHECK_REMOTE_CONNECTION timer.
        }
    }

    private void handleActorIdentity(ActorIdentity id) {

        ActorRef tempActor = id.getActorRef().get();

        // cancel the timer that checks to see if the connection timed out
        getTimers().cancel(REMOTE_CONNECTION_TIMEOUT);

        if(((int) id.correlationId()) == 1) {
            // correlationId == 1 in correlationIdMapping means that we got back a cluster shard (which is what we requested)

            if((remoteClusterShard == null) || (remoteClusterShard.isTerminated()) || (!tempActor.equals(remoteClusterShard))) {
                logger.warn("The remote actor was identified as a legitimate cluster shard!");

                if(remoteClusterShard == null) logger.warn("The sharedParentActor was null - it is now set.");
                else if(remoteClusterShard.isTerminated()) logger.warn("The sharedParentActor was terminated - re-setting the link for the remote actor.");
                else logger.warn("The sharedParentActor returned in the identity process was different - its possible the remote system re-started. Re-setting the link for the remote actor.");
                remoteClusterShard = tempActor;
            } else {
                // The 'else' here is that the remoteClusterShard actor did not have to be re-set - so do nothing, Its best to NOT re-set a remote ActorRef if you do not have to.
                logger.info("Remote actor check complete - no changes required - still [{}].", tempActor.path());
            }

        } else if(((int) id.correlationId()) == 2) {
            // If the ID is 2, it means a PROXY was hit instead of a primary cluster node - it should still work.

            if((remoteClusterShard == null) || (remoteClusterShard.isTerminated()) || (!tempActor.equals(remoteClusterShard))) {
                logger.warn("The remote actor was identified as a proxy!");

                if(remoteClusterShard == null) logger.warn("The sharedParentActor was null - it is now set.");
                else if(remoteClusterShard.isTerminated()) logger.warn("The sharedParentActor was terminated - re-setting the link for the remote actor.");
                else logger.warn("The sharedParentActor returned in the identity process was different - its possible the remote system re-started. Re-setting the link for the remote actor.");
                remoteClusterShard = tempActor;
            } else {
                // The 'else' here is that the remoteClusterShard actor did not have to be re-set - so do nothing, Its best to NOT re-set a remote ActorRef if you do not have to.
                logger.info("Remote actor check complete - no changes required - still [{}].", tempActor.path());
            }

        } else {
            logger.warn("The sharedParentActor was not returned in the Identify request!");
        }
    }

    public void remoteConnectionTimeout() {
        logger.error("The remote connection timer expired! The remote connection may be down!");
    }


    @Override
    public Receive createReceive() {
        return receiveBuilder()
                .match(ActorIdentity.class, id -> id.getActorRef().isPresent(), id -> {
                    handleActorIdentity(id);
                })
                .match(WindMyArm.class, msg -> {
                    throwFruit();
                })
                .match(CheckRemoteConnection.class, msg -> {
                    checkRemoteConnection();
                })
                .match(RemoteConnectionTimeout.class, msg -> {
                    remoteConnectionTimeout();
                })
                .build();
    }
}

```  

## LocalAppleCatcherActor.java  

This file (<font color="green">LocalAppleCatcherActor.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.actor.AbstractActor;
import akka.actor.Props;
import com.wagenseller.msg.fruit.SerializedClasses;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * This actor acts as a local actor that is unaffiliated with any cluster. If the host is a catcher (cluster-roles="FruitCatcher"), it will launch this actor, which will catch thrown apples.
 *
 */

public class LocalAppleCatcherActor extends AbstractActor {

    private static final Logger logger = LoggerFactory.getLogger(LocalAppleCatcherActor.class);
    private final String myID;

    static public Props props(int myID) {
        return Props.create(LocalAppleCatcherActor.class, () -> new LocalAppleCatcherActor(myID));
    }

    public LocalAppleCatcherActor(int myID) {
        this.myID = String.valueOf(myID);
    }

    @Override
    public Receive createReceive() {
        return receiveBuilder()

                .match(SerializedClasses.Apple.class, theApple -> {
                    logger.info("Host [{}] caught apple #[{}]", myID, theApple.getAppleNumber());
                })
                .build();
    }
}
```  

## LocalOrangeCatcherActor.java  

This file (<font color="green">LocalOrangeCatcherActor.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.actor.AbstractActor;
import akka.actor.Props;
import com.wagenseller.msg.fruit.SerializedClasses;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * This actor acts as a local actor that is unaffiliated with any cluster. If the host is a catcher (cluster-roles="FruitCatcher"), it will launch this actor, which will catch thrown oranges.
 *
 */

public class LocalOrangeCatcherActor extends AbstractActor {
    private static final Logger logger = LoggerFactory.getLogger(LocalOrangeCatcherActor.class);
    private final String myID;

    static public Props props(int myID) {
        return Props.create(LocalOrangeCatcherActor.class, () -> new LocalOrangeCatcherActor(myID));
    }

    public LocalOrangeCatcherActor(int myID) {
        this.myID = String.valueOf(myID);
    }

    @Override
    public AbstractActor.Receive createReceive() {
        return receiveBuilder()

                .match(SerializedClasses.Orange.class, theOrange -> {
                    logger.info("Host [{}] caught orange #[{}]", myID, theOrange.getOrangeNumber());
                })
                .build();
    }
}
```  

## ShardedFruitCatcher.java  

This file (<font color="green">ShardedFruitCatcher.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.actor.*;
import akka.cluster.sharding.ShardRegion;
import com.wagenseller.msg.fruit.SerializedClasses;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.Objects;

/**
 * This is the entity that will be a launched by the fruit cluster shard coordinator. A unique ShardedFruitCatcher entity is created for every unique piece of fruit thrown (and every piece has
 * a unique ID, so every piece of fruit is unique).
 *
 */

public class ShardedFruitCatcher extends AbstractActorWithTimers {

    private static final Logger logger = LoggerFactory.getLogger(ShardedFruitCatcher.class);

    // This name uniquely identifies the fruit catcher cluster family
    public static final String CLUSTER_NAME = "shardedFruitCatcher";

    ActorRef localAppleCatcher;
    ActorRef localOrangeCatcher;

    int port;
    int timeToDie;
    int messagesProcessed;
    long bornOnTime;

    boolean passivateOn;
    boolean printStartAndStopMessages;

    // These parameters facilitate this actor shutting down IF passivate is off
    private static final class EndItAll {}
    private static Object END_IT_ALL = "END_IT_ALL";


    static public Props props(ActorRef localAppleCatcher, ActorRef localOrangeCatcher, int port, int timeToDie) {
        return Props.create(ShardedFruitCatcher.class, () -> new ShardedFruitCatcher(localAppleCatcher, localOrangeCatcher, port, timeToDie));
    }

    public void preStart() {
        // determine if passivate is on or off
        String passivate = context().system().settings().config().getString("akka.cluster.sharding.passivate-idle-entity-after");
        if (passivate.toLowerCase().equals("off")) passivateOn = false;
        else passivateOn = true;

        // save the boolean that determines if we should print actor starts and stps
        printStartAndStopMessages = context().system().settings().config().getBoolean("additional.print-actor-start-and-stops");


        if (printStartAndStopMessages) logger.info("#######################Parent [{}] (using port [{}]) started! Value: [{}] #########################", self().path(), port, messagesProcessed);

        // if passivate is off, enable the ending timer
        if (passivateOn) getTimers().startSingleTimer(END_IT_ALL, new EndItAll(), java.time.Duration.ofMillis(timeToDie));
    }

    public void postStop() {

        if (printStartAndStopMessages) logger.info("Parent [{}] (using port [{}]) stopped!", self().path(), port);
    }

    public void tellEndItAll(EndItAll endIt) {
        /*
        Shut down this actor. Note that if akka.cluster.sharding.remember-entities is set to 'off', this line would work:
        self().tell(PoisonPill.getInstance(), self());

        However, if akka.cluster.sharding.remember-entities is set to 'on', this actor MUST be killed like this - its possible it MUST be sent from this actor itself, but at the very least, this
        actor MUST be the sender() AND it must be a poisonpill wrapped in an ShardRegion.Passivate(PoisonPill.getInstance()) object
        */
        getContext().getParent().tell(new ShardRegion.Passivate(PoisonPill.getInstance()), getSelf());

    }


    public ShardedFruitCatcher(ActorRef localAppleCatcher, ActorRef localOrangeCatcher, int port, int timeToDie) {

        this.localAppleCatcher = localAppleCatcher;
        this.localOrangeCatcher = localOrangeCatcher;
        this.port = port;
        this.timeToDie = timeToDie;
        this.bornOnTime = System.currentTimeMillis();

    }

    public void tellLocalAppleCatcherToCatchTheApple(SerializedClasses.Apple theApple) {
        messagesProcessed++;
        if (messagesProcessed == 1) logger.info("Apple message received. Delta (in ms) between Actor Startup and message received: [{}]", System.currentTimeMillis() - this.bornOnTime);
        localAppleCatcher.tell(theApple, self());

    }

    public void tellLocalOrangeCatcherToCatchTheApple(SerializedClasses.Orange theOrange) {
        messagesProcessed++;
        if (messagesProcessed == 1) logger.info("Orange message received. Delta (in ms ) between Actor Startup and message received: [{}]", System.currentTimeMillis() - this.bornOnTime);
        localOrangeCatcher.tell(theOrange, self());
    }

    @Override
    public Receive createReceive() {
        return receiveBuilder()
                .match(EndItAll.class, endIt -> {
                    tellEndItAll(endIt);
                })
                .match(SerializedClasses.Apple.class, theApple -> {
                    tellLocalAppleCatcherToCatchTheApple(theApple);
                })
                .match(SerializedClasses.Orange.class, theOrange -> {
                    tellLocalOrangeCatcherToCatchTheApple(theOrange);
                })
                .build();
    }

    // Its not always possible to write your own hashing function, so do so here - we assume the ID is unique
    static int entityIdToHash(String ID) {
        return Objects.hash(ID);
    }

    /**
     * Each and every entity grouping that will be used in cluster shards MUST have a messageExtractor(), and the messageExtractor() MUST have these three methods:
     *  1. entityId - This will get the unique identifier.
     *  2. entityMessage - This will either forward on the message OR extract the message from an envelope.
     *  3.
     *
     * @param maxShards
     * @return
     */
    static ShardRegion.MessageExtractor messageExtractor(int maxShards) {
        return new ShardRegion.MessageExtractor() {

            /*
            It is expected that every entity will have a unique identifier; furthermore, every message will have a unique identifier, and that identifier will reference the entityId.
            The unique identifier for the fruit shards is the fruitIdentifier - so the fruitIdentifier is returned.
             */
            @Override
            public String entityId(Object message) {
                if (message instanceof SerializedClasses.FruitEnvelope) {

                    int identifierInEnvelope = ((SerializedClasses.FruitEnvelope) message).getFruitIdentifier();
                    return (identifierInEnvelope + "");
                } else {
                    return null;
                }
            }

            /*
                Since the expected message that this entity will receive is SerializedClasses.Orange or SerializedClasses.Apple, we need a way to extract this message from the envelope so the
                entity's parent can forward on the message to this entity.
             */
            @Override
            public Object entityMessage(Object message) {

                if (message instanceof SerializedClasses.FruitEnvelope) {
                    SerializedClasses.FruitEnvelope envelope = ((SerializedClasses.FruitEnvelope) message);

                    //if (envelope.getMessage() instanceof Apple)
                    if (envelope.getMessage().is(SerializedClasses.Apple.class)) {
                        try {
                            // returns an Apple type
                            return envelope.getMessage().unpack(SerializedClasses.Apple.class);
                        } catch (com.google.protobuf.InvalidProtocolBufferException e) {
                            logger.error("Said it was an Apple, but was not.");
                        }

                        return null;

                    } else if (envelope.getMessage().is(SerializedClasses.Orange.class)) {
                        try {
                            // returns an Orange type
                            return envelope.getMessage().unpack(SerializedClasses.Orange.class);
                        } catch (com.google.protobuf.InvalidProtocolBufferException e) {
                            logger.error("Said it was an Orange, but was not.");
                        }

                        return null;
                    } else {
                        return null;
                    }
                } else {
                    return null;
                }
            }

            /**
             * This figures out the shard where the specific entity will reside.
             *
             * Note: **BOTH THE 'IF' AND 'ELSE IF' HERE MUST RETURN THE SAME HASH VALUE FOR A SINGULAR UNIQUE ID! If it does not, messages will be queued indefinitely.
             *
             * @param message
             * @return
             */
            @Override
            public String shardId(Object message) {

                String retVal;
                if (message instanceof SerializedClasses.FruitEnvelope) {
                    retVal = String.valueOf(Math.abs(entityIdToHash(((SerializedClasses.FruitEnvelope) message).getFruitIdentifier() + "") % maxShards));
                    return retVal;
                } else if (message instanceof ShardRegion.StartEntity) {

                    int hashedEntityID = entityIdToHash(((ShardRegion.StartEntity) message).entityId());
                    retVal = String.valueOf(hashedEntityID % maxShards);
                    return retVal;
                } else {
                    return null;
                }
            }
        };
    }
}
```  