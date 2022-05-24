# Akka gRPC Example (Using Actors)

# Overview  

This is a basic Akka example that uses gRPC as communication between a client and a server; in this case, both the client and server are Akka actors. The basic premise of this project is: the client requests that the server throw it some specific fruit; the server complies, and after it throws 5 it stops throwing the client fruit. 

It uses [Maven](learn_to_code/java/maven) to build the project; in addition, it uses [protobuf](learn_to_code/java/protobuf), so please have that installed.

!> This entire project assumes the Java package is `com.wagenseller`; feel free to change, but make sure to change it everywhere.  


# pom.xml 

This is the Maven [pom](learn_to_code/java/akka/akka_basic_examples?id=pomxml) file for the project:  
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.wagenseller</groupId>
  <artifactId>grpc-test-actorref-comm</artifactId>
  <packaging>jar</packaging>
  <version>1.0.0-SNAPSHOT</version>
  <name>grpc-test-actorref-comm</name>
  <url>http://maven.apache.org</url>
  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <akka.version>2.6.13</akka.version>
    <akka.http.version>10.2.4</akka.http.version>
    <akka.grpc.version>1.1.1</akka.grpc.version>
    <scala.binary.version>2.13</scala.binary.version>
    <grpc.version>1.28.1</grpc.version>
    <project.encoding>UTF-8</project.encoding>
  </properties>

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

        <!--
             The akka grpc plugin takes care of protobuf, so we do not need to set that up - matter of fact there are detractions to including the
             usual protobuf plugin:
             * It seems to generate an unnecessary duplicate classes.
             * By itself, protobuf can handle a service - however its pretty rudimentary.  Many of the needed generated functionality is not done by Protobuf natively -
               so use Akka's version embedded in their Maven plugin. Things not generated seem to be:
               * The service class as a standalone (i.e. FruitService)
               * The XXXServiceClient class (i.e. FruitServiceClient)
               * The XXXServiceClientPowerApi class (i.e. FruitServiceClientPowerApi)
               * The XXXServiceHandlerFactory class (i.e. FruitServiceHandlerFactory)


             In addition, this takes care of many extra things - it creates a bunch of additional helper generated classes
             in target/generated-sources/akka-grpc-java
        -->

        <plugin>
          <groupId>com.lightbend.akka.grpc</groupId>
          <artifactId>akka-grpc-maven-plugin</artifactId>
          <version>${akka.grpc.version}</version>
          <configuration>
            <language>Java</language>
          </configuration>
          <executions>
            <execution>
              <goals>
                <goal>generate</goal>
              </goals>
            </execution>
          </executions>
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
      </plugins>
  </build>
  <dependencies>

    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-http_${scala.binary.version}</artifactId>
      <version>${akka.http.version}</version>
    </dependency>

    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-http2-support_${scala.binary.version}</artifactId>
      <version>${akka.http.version}</version>
    </dependency>


    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-discovery_${scala.binary.version}</artifactId>
      <version>${akka.version}</version>
    </dependency>

    <dependency>
      <groupId>com.lightbend.akka.grpc</groupId>
      <artifactId>akka-grpc-runtime_${scala.binary.version}</artifactId>
      <version>${akka.grpc.version}</version>
    </dependency>

    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-actor-typed_${scala.binary.version}</artifactId>
      <version>${akka.version}</version>
    </dependency>

    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-stream_${scala.binary.version}</artifactId>
      <version>${akka.version}</version>
    </dependency>

    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-pki_${scala.binary.version}</artifactId>
      <version>${akka.version}</version>
    </dependency>

    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-actor-testkit-typed_${scala.binary.version}</artifactId>
      <version>${akka.version}</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-stream-testkit_${scala.binary.version}</artifactId>
      <version>${akka.version}</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.1</version>
      <scope>test</scope>
    </dependency>

    <!-- Needed for the generated client -->
    <dependency>
      <groupId>io.grpc</groupId>
      <artifactId>grpc-stub</artifactId>
      <version>${grpc.version}</version>
    </dependency>

  </dependencies>
</project>
```  
* This assumes the [protobuf](learn_to_code/java/protobuf) binary file is located at `/usr/local/bin/protoc`; if this is not where it is located, please point the variable `protobuf.executable` to the correct binary!  


# application.conf

This is the [application.conf](learn_to_code/java/akka/akka_installation_and_config_files?id=applicationconf) file; it should go in your [resources](learn_to_code/java/maven?id=resources-in-maven) folder.  
``` 
# For more config options for Akka HTTP, see: https://doc.akka.io/docs/akka-http/current/configuration.html

akka {
  http {
    server {
        preview {
            # 'akka.http.server.preview.enable-http2' is required (although, in the configuration docs, this says its 'not fully production ready', but it
            # is absolutely necessary). If this is _NOT_ present in the actor system config, it will not work and you will get a warning similar to this:
            # WARN akka.actor.ActorSystemImpl akka.actor.ActorSystemImpl(ActorSystem) Illegal request, responding with status '501 Not Implemented': Unsupported HTTP method: PRI
            enable-http2 = on
        }

        # This is the only parameter that is not clear to me, but it has something to do with multiple concurrent requests with a single http2 connection
        # to the server
        # pipelining-limit defaults to 1
        pipelining-limit = 8

        # Enables/disables the addition of a remote-address attribute in HttpRequest
        # holding the clients (remote) IP address. This is preferred over `remote-address-header`
        # because it cannot be confused with a real header.
        # This is not necessary, but I like to use it.
        remote-address-attribute = on

        # This setting controls the idle timeout on the server side (I am not sure there is one for the client - removing this for the client had no effect,
        # though, so it may not be needed on the client); minutes are set like so (for seconds, replace with an 's'):
        # idle-timeout = 10m
        # To set it to infinite, you would do this:
        # idle-timeout = infinite
        idle-timeout = 5m
    }

    client {
        # same as the server idle-timeout, but for the client
        idle-timeout = 5m
    }

    host-connection-pool {

        # The number of open / active requests allowed across all materializations. Note this is _not_ the same thing as concurrent connections: it seems
        # this ONLY counts connections that are not fully established. This MUST be a power of 2!
        max-open-requests = 4

        # The number of requests that can be open per foreign host. If you are only connecting to one host with 256 simultaneous connections, if this is
        # set less than 256 you will have issues. If, in theory, you were connecting to 256 foreign hosts and you are guaranteed only one connection per
        # foreign host, setting this to 4 is acceptable.
        max-connections = 50

    }
  }
}
```  

# logback.xml

This project uses [logback](learn_to_code/java/logback); this is the setup file [logback.xml](learn_to_code/java/logback?id=logbackxml); it should go in your [resources](learn_to_code/java/maven?id=resources-in-maven) folder.  

```
<configuration scan="false" debug="true">
    <conversionRule conversionWord="traceToken" converterClass="kamon.trace.logging.LogbackTraceTokenConverter"/>
    <statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />
    <property file="/etc/log.properties" />

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS,UTC} %highlight(%level) [%thread] %cyan(%logger{90}) %magenta(%X{akkaSource}) %msg%n</pattern>
        </encoder>
    </appender>

    <!-- This is important to control, otherwise the DEBUG will absolutely spam your log. -->
    <logger name="io.grpc.netty.shaded.io.grpc.netty.NettyClientHandler" level="${LOGLEVEL_GRPC_NETTY:-INFO}" additivity="false">
        <appender-ref ref="STDOUT"/>
    </logger>

    <!-- This will spam the initial setup, but after that it seems OK. -->
    <logger name="io.grpc.netty.shaded.io.netty.handler.ssl.CipherSuiteConverter" level="${LOGLEVEL_NETTY_CIPHERSUITE:-INFO}" additivity="false">
        <appender-ref ref="STDOUT"/>
    </logger>

    <!-- This will spam the initial setup, but after that it seems OK. Its annoying as it seems to kick off errors (marked as DEBUG) when it cannot load
    a library path, but its _expected_ to not load some library paths and _should_ eventually find one that works.  -->
    <logger name="io.grpc.netty.shaded.io.netty.util.internal.NativeLibraryLoader" level="${LOGLEVEL_NETTY_NATIVELIBRARYLOADER:-INFO}" additivity="false">
        <appender-ref ref="STDOUT"/>
    </logger>



    <logger name="com.wagenseller" level="${LOGLEVEL_WAGENSELLER:-DEBUG}" additivity="false">
        <appender-ref ref="STDOUT"/>
    </logger>


    <root level="DEBUG">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```  

# fruitservice.proto  


This file (<font color="green">SerializedClasses.proto</font>) is the [protobuf](learn_to_code/java/protobuf) class file and should go in the `src/main/proto/` directory. Running `mvn clean install` in the main directory of the project (where the pom file resides) will generate the necessary classes defined in this file.  

```
syntax = "proto3";

option java_package = "com.wagenseller";
option java_outer_classname = "HealthServiceMsg";
option optimize_for = CODE_SIZE;


service FruitService {

    // The stream of incoming FruitRequest messages are sent out as corresponding FruitResponse - from all clients to all clients.
    rpc StreamSomeFruitBetweenActors (stream FruitRequest) returns (stream FruitResponse) {}

}


// The request message containing the user's requested fruit type.
message FruitRequest {
    string fruitType = 1;
}

// The response message containing the fruit type and its ID
message FruitResponse {
    string individualFruit = 1;
}

// This is used to end the fruit requests
message EndFruit { }
```  

# MyMain.java  

This file (<font color="green">MyMain.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  
```
package com.wagenseller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.Locale;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 *
 * This facilitates message passing between two remote actors via a gRPC stream. There are two 'modes' to this application: one that acts as a server that
 * accepts a connection over gRPC, and a client that sends said connections (you can select which mode via a parameter - see the 'To run' section below).
 *
 *
 * To run:
 * Server: java -cp target/grpc-test-actorref-comm-1.0.0-SNAPSHOT.jar com.wagenseller.MyMain -Drole=server
 * Client: java -cp target/grpc-test-actorref-comm-1.0.0-SNAPSHOT.jar com.wagenseller.MyMain -Drole=client
 *
 * References:
 * * Akka HTTP Configuration - https://doc.akka.io/docs/akka-http/current/configuration.html
 *
 * Notes:
 * * gRPC uses HTTP2
 **/
public class MyMain {

    private static final Logger logger = LoggerFactory.getLogger(MyMain.class);

    public static void main(String[] args) throws Exception {
        String role = getRoleFromArgs(args);

        // startup either a client or server
        if (role.equals("server")) new Server().run();
        else new Client().run();
    }

    /**
     * This parses the arguments from the command line by using a regex pattern matching schema to extract each parameter and value.
     *
     * @param args
     */
    private static String getRoleFromArgs(String[] args) {
        String regex = "-D(\\S+)=(\\S+)";
        String role = "";
        Pattern pattern = Pattern.compile(regex);
        for (String arg : args) {
            Matcher matcher = pattern.matcher(arg);

            // for every pattern matched, extract the key / value and save
            // (in our case, simply get the 'role' and set it to lowercase)
            while(matcher.find()) {
                String key = matcher.group(1);
                String value = matcher.group(2);
                if(key.toLowerCase(Locale.ROOT).equals("role")) role = value.toLowerCase(Locale.ROOT);
            }
        }

        // If the role was not defined, set this to 'server'
        if (role.equals("")) {
            role = "server";
            logger.info("Role not defined - defaulting to '{}'", role);
        }

        return role;
    }
}

```  

# Client.java  

This file (<font color="green">Client.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.actor.ActorRef;
import akka.actor.ActorSystem;
import akka.stream.Materializer;
import akka.stream.SystemMaterializer;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;


public class Client {

    private static final Logger logger = LoggerFactory.getLogger(Client.class);

    public void run() {
        final ActorSystem system = ActorSystem.create("FruitClient");
        Materializer mat = SystemMaterializer.get(system).materializer();

        ActorRef newAppleActor=system.actorOf(FruitClientActor.props("apple", mat));
        //ActorRef newOrangeActor=system.actorOf(FruitClientActor.props("orange", mat));
        //ActorRef newPearActor=system.actorOf(FruitClientActor.props("pear", mat));
        //ActorRef newBananaActor=system.actorOf(FruitClientActor.props("banana", mat));
        //ActorRef newMangoActor=system.actorOf(FruitClientActor.props("mango", mat));
        //ActorRef newPeachActor=system.actorOf(FruitClientActor.props("peach", mat));
        
    }
}
```  

# Server.java  

This file (<font color="green">Server.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.actor.ActorSystem;
import akka.http.javadsl.Http;
import akka.http.javadsl.ServerBinding;
import akka.stream.Materializer;
import akka.stream.SystemMaterializer;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.CompletionStage;

/**
 * This sets up a server actor system
 */
public class Server {

    final ActorSystem system;

    private ServerBinding binding;

    private static final Logger logger = LoggerFactory.getLogger(Server.class);

    public Server() {

        system = ActorSystem.create("FruitServer");

    }

    public void run() {

        // Creates a service, which accepts a 'HttpRequest' as an input and outputs a 'CompletionStage<HttpResponse>'
        // 'service' is a Function lambda that is required in an output that feeds an akka.http.javadsl.Http object (in our case, 'bound), which sets up the
        // HTTP2 service. Note that the FruitServiceHandlerFactory was NOT created by us - it was created by akka-grpc-maven-plugin (see the pom.xml file), which
        // uses the .proto file to build this. All we have to do is create the implementation (in our case, FruitServiceImpl).

        Materializer mat = SystemMaterializer.get(system).materializer();

        FruitServiceImpl fruitServiceImpl = new FruitServiceImpl(system, mat);

        // attempt to bind the HTTP server to the given IP and port.
        CompletionStage<ServerBinding> futureFruit = Http.get(system)
                .newServerAt("127.0.0.1", 8080)
                .bind(FruitServiceHandlerFactory.create(fruitServiceImpl, system))
                .thenApply(binding -> this.binding=binding);

        // To stop the ENTIRE server, you would need to interact with the binding a la
        // binding.terminate(Duration.ofSeconds(3));
    }
}
```  

# MaterializedDestination.java  

This file (<font color="green">MaterializedDestination.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.actor.ActorRef;

public class MaterializedDestination {
    // This is needed as a class that can handle the materialized actorRef is needed - so we write that small class here.

    private final ActorRef materializedActorRef;

    public MaterializedDestination(ActorRef materializedActorRef) {
        this.materializedActorRef = materializedActorRef;
    }

    public ActorRef getMaterializedActorRef() {
            return materializedActorRef;
        }

}
```  

# FruitServiceImpl.java  

This file (<font color="green">FruitServiceImpl.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.Done;
import akka.NotUsed;
import akka.actor.ActorRef;
import akka.actor.ActorSystem;
import akka.stream.Attributes;
import akka.stream.CompletionStrategy;
import akka.stream.Materializer;
import akka.stream.OverflowStrategy;
import akka.stream.javadsl.*;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.Optional;


/**
 * The akka-grpc-maven-plugin (see the pom.xml file) uses the .proto file to build multiple helper files that are used in gRPC; that said, there is a requirement
 * to write an 'implementation' that implements the service as its defined in the .proto file (in our case, this is the service 'FruitService').
 * All we have to do is create the implementation (in our case, FruitServiceImpl).
 */

public class FruitServiceImpl implements FruitService {

    private static final Logger logger = LoggerFactory.getLogger(FruitServiceImpl.class);
    protected static final int MIN_INPUT_BUFFER = 16;
    protected static final int MAX_INPUT_BUFFER = 64;

    final ActorSystem system;
    final Materializer materializer;

    public FruitServiceImpl(ActorSystem system, Materializer materializer) {
        this.system = system;
        this.materializer = materializer;

    }

    /**
     * This implements 'StreamSomeFruitBetweenActors', which will run on the server for stream requests.
     * @param theStream
     * @return
     */
    @Override
    public Source<HealthServiceMsg.FruitResponse, NotUsed> streamSomeFruitBetweenActors(Source<HealthServiceMsg.FruitRequest, NotUsed> theStream) {
        // create the session actor
        ActorRef newFruitActor=system.actorOf(FruitServerActor.props());

        // wire the inbound requests to the actor
        theStream.runWith(Sink.actorRef(newFruitActor, Done.done()), materializer);
        // give that actor an outbound destination for sending responses, return that as a source

        // we need to use a method here and cannot just paste the contents of the method 'materializeSourceActor' here - if not, its marked as a
        // 'Source<Object, ActorRef>' and not a 'Source<FruitResponse, ActorRef>' which throws an error.
        return materializeSourceActor()
                // Source.actorRef() will create a source actorRef out of the materializer; we cannot code this actor directly, but we CAN interact with it
                // indirectly through another actor - and that actor is newFruitActor (above). We routed all inbound messages to interact with newFruitActor
                // in the 'theStream.runWith()' line above, but this is only half the story - we need to tell newFruitActor about the new actor from the materializer
                // so it can pass the messages it receives to said materialized actor (which will automatically forward all messages it gets). This is done below
                // in mapMaterializedValue(), which is run _after_ the materialized actor is created (and its actorRef is sent to this method as a parameter).
                .mapMaterializedValue(destinationRef -> {
                    logger.debug("A new fruit actor [{}] was created for the materialized actor [{}]", newFruitActor.path(), destinationRef.path());
                    newFruitActor.tell(new MaterializedDestination(destinationRef), ActorRef.noSender());

                    // SOMETHING must be returned, and since we are basically discarding the materialized value after this (as dictated by the 'NotUsed'
                    // in the returned materialized value in 'streamSomeFruitBetweenActors') we return the below
                    return NotUsed.getInstance();
                })
                .withAttributes(Attributes.inputBuffer(MIN_INPUT_BUFFER, MAX_INPUT_BUFFER));
    }

    protected Source<HealthServiceMsg.FruitResponse, ActorRef> materializeSourceActor() {
        return Source.actorRef(FruitServiceImpl::completeOnDone,
                FruitServiceImpl::thereIsNoFail,
                100,
                OverflowStrategy.dropNew());
    }

    public static Optional<CompletionStrategy> completeOnDone(Object elem) {
        return elem == Done.done() ?
                Optional.of(CompletionStrategy.immediately()) :
                Optional.empty();
    }

    public static Optional<Throwable> thereIsNoFail(Object elem) {
        return Optional.empty();
    }

}
```  

# FruitServerActor.java  

This file (<font color="green">FruitServerActor.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.Done;
import akka.actor.*;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.Locale;

/**
 * The basic flow of the server - in Server.java:
 * 1. Start the server actor system.
 * 2. Define FruitServiceImpl, which handles incoming requests
 * 2. Bind the FruitServiceImpl to this IP, port 8080 - after this is done we are now waiting for incoming requests.
 *
 * When an incoming request does come in, this happens (mostly in this java file):
 * 1. FruitServiceImpl.streamSomeFruitBetweenActors() is activated
 *  A. A FruitServerActor is created for this request
 *  B. An _additional_ materialized actor is created for this request (as needed by FruitServiceImpl.streamSomeFruitBetweenActors)
 * 2. the FruitServerActor gets the ActorRef of the materialized actor; FruitServerActor sends all messages intended for the client to the materialized
 *    actor and the materialized actor forwards these messages on to the client.
 * 3. Messages can now be passed back and forth between the client and server.
 */
public class FruitServerActor extends AbstractActorWithTimers {

    private static final Logger logger = LoggerFactory.getLogger(FruitServerActor.class);

    private String fruitType;

    // Counts this specific fruit type thrown. If 5 or more, we stop
    private int individualFruitNumber;

    private ActorRef outboundActor;

    private boolean alreadySentDone = false;

    //this message will be trigger fruit to be sent every 5 seconds
    private static final class SendAPieceOfFruit {}
    private static final Object SEND_A_PIECE_OF_FRUIT = "SEND_A_PIECE_OF_FRUIT";


    public FruitServerActor() {
        individualFruitNumber = 0;
    }

    public static Props props() {
        return Props.create(FruitServerActor.class, () -> new FruitServerActor());
    }

    public void preStart() {
        logger.info("Starting server actor for fruit type [{}]!", fruitType);

    }

    public void postStop() {
        logger.info("Stopping server actor for fruit type [{}]!", fruitType);
    }

    //This message is sent in FruitServiceImpl's '.mapMaterializedValue()' during the stream setup; there, the materialized actorRef is sent to this actor.
    // We capture that actorRef here.
    private void setOutboundActor(MaterializedDestination msg) {
        outboundActor = msg.getMaterializedActorRef();
    }

    private void handleStatusFailure(Status.Failure msg) {
        logger.warn("Uh oh! It seems the remote actor closed without telling us, as we received a 'Status.Failure'. Shutting down server with fruit type [{}]!", fruitType);
        self().tell(PoisonPill.getInstance(), self());
    }

    private void handleDone(Done msg) {
        logger.warn("It seems the client is full and cannot eat any more of fruit type [{}]! Closing actors!", fruitType);
        self().tell(PoisonPill.getInstance(), self());

    }

    // This should only be called once per client instance.
    private void onFruitRequest(HealthServiceMsg.FruitRequest request) {

        fruitType = request.getFruitType();
        logger.info("Received new fruit request in [{}] for fruit [{}]", self().path().name(), fruitType);

        // start the timer which will send a piece of fruit at regular intervals
        getTimers().startTimerAtFixedRate(SEND_A_PIECE_OF_FRUIT, new SendAPieceOfFruit(), java.time.Duration.ofMillis(5000));

    }

    private void handleSendAPieceOfFruit(SendAPieceOfFruit msg) {

        // if we have thrown 5 pieces of fruit, end the fruit throwing
        if (individualFruitNumber >=5) {
            logger.info("All out of fruit type [{}]! Notifying client we are done", fruitType, individualFruitNumber);

            outboundActor.tell(Done.getInstance(), getSelf());
            //outboundActor.tell(Done.done(), getSelf());

        } else {
            HealthServiceMsg.FruitResponse returnedMsg = HealthServiceMsg.FruitResponse.newBuilder()
                    .setIndividualFruit(fruitType.toUpperCase(Locale.ROOT) + ++individualFruitNumber)
                    .build();

            logger.info("Throwing fruit type [{}] (number [{}]) to the client!", fruitType, individualFruitNumber);

            outboundActor.tell(returnedMsg, getSelf());
        }
    }

    public Receive createReceive() {
        return receiveBuilder()
                .match(HealthServiceMsg.FruitRequest.class, this::onFruitRequest)
                .match(MaterializedDestination.class, this::setOutboundActor)
                .match(SendAPieceOfFruit.class, this::handleSendAPieceOfFruit)
                .match(Status.Failure.class, this::handleStatusFailure)
                .match(Done.class, this::handleDone)
                .build();
    }
}
```  

# FruitClientActor.java  

This file (<font color="green">FruitClientActor.java</font>) should go in the `main/java/com/wagenseller` directory (or whatever package you chose to use).  

```
package com.wagenseller;

import akka.Done;
import akka.NotUsed;
import akka.actor.*;
import akka.grpc.GrpcClientSettings;
import akka.stream.Materializer;
import akka.stream.OverflowStrategy;
import akka.stream.javadsl.Sink;
import akka.stream.javadsl.Source;
import com.google.protobuf.Message;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * The basic flow of the client:
 * 1. Create a new FruitClientActor (in Client.java)
 * 2. FruitClientActor creates the gRPC client (with FruitServiceClient.create())
 *  A. This materializes a secondary actor
 *  B. FruitClientActor gets the ActorRef of this new materialized actor; if FruitClientActor wishes to interact with the server, it sends messages through
 *     this actor.
 * 3. FruitClientActor binds itself to the stream, which accepts messages fro mthe server.
 */
public class FruitClientActor extends AbstractActorWithTimers {

    private static final Logger logger = LoggerFactory.getLogger(FruitClientActor.class);

    String fruitType;
    Materializer materializer;

    int fruitEaten = 0;
    private final int MAX_FRUIT_TO_EAT = 30;

    private ActorRef outboundActor;

    private boolean alreadySentDone = false;

    private FruitServiceClient client;

    public FruitClientActor(String fruitType, Materializer materializer) {
        this.fruitType = fruitType;
        this.materializer = materializer;

    }

    public static Props props(String fruitType, Materializer materializer) {
        return Props.create(FruitClientActor.class, () -> new FruitClientActor(fruitType, materializer));
    }

    public void preStart() {
        logger.info("Starting client actor for fruit type [{}]!", fruitType);

        GrpcClientSettings settings = GrpcClientSettings.connectToServiceAt("127.0.0.1", 8080, context().system())
                //.withUserAgent("FruitClient")
                .withTls(false);

        // Create the fruit service client
        client = FruitServiceClient.create(settings, getContext().getSystem());

        // This gets the request source by materializing a source actor
        Source<HealthServiceMsg.FruitRequest, NotUsed> requests = materializeSourceActor();


        // The above materializes an actor that will accept the incoming messages from a stream - more specifically, its an actor that is _also_ a
        // stream source.
        // Connect this materialized source actor to the client's responses
        Source<HealthServiceMsg.FruitResponse, NotUsed> responses = client.streamSomeFruitBetweenActors(requests);

        // have these responses mapped back to a _specific_ method - in our case, when we get a message, we will send that message to the
        // method 'handleResponseFromServer'
        // The .runWith() will forward on the message to this actor (i.e. FruitClientActor)
        responses.map(this::handleResponseFromServer)
                .runWith(Sink.actorRef(getSelf(), Done.done()), materializer);

    }

    public void postStop() {
        logger.info("Stopping client actor for fruit type [{}]!", fruitType);
    }

    /**
     * This acts as a landing for responses from the server. Really, this could be eliminated and we could simply use the method 'handleFruitResponse()'
     * in the 'responses.map().runWith()' above, but this is to showcase how this could work if you have need for multiple response types (
     * and you would presumably wrap the response in an envelope)
     *
     * This returns a value, and that value is sent back to this actor
     *
     * @param msg
     * @return
     */
    private Message handleResponseFromServer(HealthServiceMsg.FruitResponse msg) {
        logger.info("Handling response from server for fruit [{}] - processing individual fruit piece [{}].", fruitType, msg.getIndividualFruit());

        return msg;
    }

    private void handleFruitResponse(HealthServiceMsg.FruitResponse msg) {
        logger.info("Eating individual piece of fruit [{}]!", msg.getIndividualFruit());

        fruitEaten++;

        if (fruitEaten >= MAX_FRUIT_TO_EAT) {
            logger.info("I have eaten [{}] pieces of [{}] and am full - telling the server to stop sending fruit!", fruitEaten, fruitType);

            //self().tell(PoisonPill.getInstance(), self());
        }

    }

    private void handleStatusFailure(Status.Failure msg) {
        logger.warn("Uh oh! It seems the remote actor closed without telling us, as we received a 'Status.Failure'. Shutting down server with fruit type [{}]!", fruitType);
        self().tell(PoisonPill.getInstance(), self());
    }

    private void handleDone(Done msg) {
        logger.warn("It seems the server has run out of fruit type [{}] and has told us to come back later! Closing actors!", fruitType);

        client.close();
        //if (alreadySentDone)
        self().tell(PoisonPill.getInstance(), self());
    }

    /**
     * Materializes the source actor. This also calls methods that link the materialized actor to this actor (FruitClientActor) so we can interact with it.
     *
     * @return
     */
    protected Source<HealthServiceMsg.FruitRequest, NotUsed> materializeSourceActor() {

        // actually create the materialized actor
        Source<HealthServiceMsg.FruitRequest, ActorRef> actorRefSource =
                Source.actorRef(FruitServiceImpl::completeOnDone,
                        FruitServiceImpl::thereIsNoFail,
                        64,
                        OverflowStrategy.dropNew());


        // Map the materialized actor via a Function (which takes one parameter, which is the NEWLY materialized actor). We use a trick here
        // to pass the ActorRef of this FruitClientActor to this Function (see the description in 'captureMaterializedActor()' for more details); from
        // there, we can save the ActorRef of the materialized actor to this actor.
        //
        // Note the method 'captureMaterializedActor()' also transforms the shape from 'Source<HealthServiceMsg.FruitRequest, ActorRef>' to
        // 'Source<HealthServiceMsg.FruitRequest, NotUsed>', which is required for the response
        Source<HealthServiceMsg.FruitRequest, NotUsed> materializeActor = actorRefSource.mapMaterializedValue(captureMaterializedActor(self(), logger));

        return materializeActor;
    }

    /**
     * We use a trick to send this ActorRef (FruitClientActor) to a (future) akka.japi.function.Function, which actually gets the ActorRef of the materialized
     * actor (see notes for the method 'captureMaterializedActor()'). This is the method that accepts the 'MaterializedDestination' message which contains the
     * ActorRef to the materialized actor.
     *
     * This should only be called once, during the beginning of the gRPC connection setup for this particular fruit type.
     *
     * @param msg
     */
    private void setOutboundActor(MaterializedDestination msg) {

        // actually save the materialized actorRef
        outboundActor = msg.getMaterializedActorRef();

        logger.debug("Setting materialized actor to [{}] for fruit [{}]", outboundActor.path(), fruitType);

        // now that we have the materialized actorRef, we can  interact with it - send the server a request for fruit!
        logger.debug("Requesting delicious fruit: [{}]!", fruitType);

        HealthServiceMsg.FruitRequest newFruit = HealthServiceMsg.FruitRequest.newBuilder().setFruitType(fruitType).build();

        // The message goes to the server over the materialized actor
        outboundActor.tell(newFruit, self());
    }

    /**
     * This uses a modified version of 'Function', akka.japi.function.Function. akka.japi.function.Function has one parameter - the actorRef of the newly
     * materialized actor. We use a trick here - we know that whatever calls this will _eventually_ need a akka.japi.function.Function returned; however,
     * there is no guarantee it will have access to the ActorRef - or logger - of FruitClientActor. We can make this method - which is static and takes
     * and ActorRef 'thisVeryActor' and its logger as a parameter - and calls this static method. We can then use this actorRef and logger in the
     * akka.japi.function.Function call itself.
     *
     * Again, the akka.japi.function.Function's official parameter is the actorRef of the _materialized_ actor (which, note, is NOT the ActorRef passed to
     * thisVeryActor); we then pass the actorRef of the newly materialized actor to the actor we opened (thisVeryActor, i.e. the FruitClientActor)
     * in a 'MaterializedDestination' message. Ultimately, this sets 'outboundActor', which we can then pass messages to (which, subsequently, will pass
     * the message via gRPC to the actor carved out for this instance on the server).
     *
     * Also note this transforms the shape from 'Source<HealthServiceMsg.FruitRequest, ActorRef>' to 'Source<HealthServiceMsg.FruitRequest, NotUsed>', which
     * is required for the response.
     *
     * @param thisVeryActor
     * @param logger
     * @return
     */
    public static akka.japi.function.Function<ActorRef, NotUsed> captureMaterializedActor(ActorRef thisVeryActor, Logger logger) {
        return materializedActorRef -> {
            logger.debug("Notifying myself [{}] of my associated materialized actor: {}", thisVeryActor.path(), materializedActorRef.path());
            thisVeryActor.tell(new MaterializedDestination(materializedActorRef), materializedActorRef);

            // returns a materialized value of 'NotUsed'
            return NotUsed.getInstance();
        };
    }

    public Receive createReceive() {
        return receiveBuilder()
                .match(HealthServiceMsg.FruitResponse.class, this::handleFruitResponse)
                .match(MaterializedDestination.class, this::setOutboundActor)
                .match(Status.Failure.class, this::handleStatusFailure)
                .match(Done.class, this::handleDone)
                .build();
    }
}
```  