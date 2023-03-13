# gRPC Actor (With Envelopes) Example  


# About this Example

This is an example of a simple gRPC client / server, facilitating message passing between two remote actors via a gRPC stream. The messages passed back and forth utlize an envelope, which means its quite flexible in which types of message can be passed back and forth (without this envelope approach, you would have to either be bound to ONE message request type and ONE response type, _or_ you would have t owrite multiple rpcs.  

In this context, the gRPC stream is kept open an arbitrary amount of time so an arbitrary amount of messages can be passed back and forth before the connection is terminated. If you are looking for a gRPC request that is a SINGLE request and then a SINGLE response, this is not it!

## Client App  



## Server App  



# Installing this Code  

This installation assumes you are using [Maven](learn_to_code/java/maven); a review of the basic directory structure is located [here](learn_to_code/java/maven?id=locations-of-files-in-maven).  

Where to put the files in this example:  
* Place the pom.xml file in the base of the project folder (as usual for all Maven projects).  
* Put all java files in the same folder (from the base of the project folder, I used `src/main/java/com/wagenseller/`, but you are obviously free to change up that last folder if you change the associated package names).  
* Place fruitservice.proto in `src/proto/`.  
* Place application.conf and logback.xml in `src/resources`.

There are two apps here - a client and a server. They are run from the same jar file, just with different roles. To run the server (if you are in the base project directory):
```
java -cp target/grpc-test-using-envelopes-1.0.jar com.wagenseller.MyMain -Drole=server
```  

And to run the client (if you are in the base project directory):
```
java -cp target/grpc-test-using-envelopes-1.0.jar com.wagenseller.MyMain -Drole=client
```  

# Common Files  

These files are common between the client and server.  

## pom.xml  

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.wagenseller</groupId>
  <artifactId>grpc-test-using-envelopes</artifactId>
  <packaging>jar</packaging>
  <version>1.0</version>
  <name>grpc-test-using-envelopes</name>
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
    <resources>
      <!--  Resource configuration for git-commit-id-plugin -->
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
        <includes>
          <include>**/*.map</include>
          <include>**/*.conf</include>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
      </resource>
    </resources>
    <plugins>
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
          <generateClient>true</generateClient>
          <generateServer>true</generateServer>
          <includeStdTypes>true</includeStdTypes> <!-- This MUST be present IF we are including 'google/protobuf/any.proto' in the .proto file (and we are) -->
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

    <!--
    These are the 'normal' protobuf dependencies, but these are included in the akka-grpc-maven-plugin
    <dependency>
      <groupId>com.google.protobuf</groupId>
      <artifactId>protobuf-java</artifactId>
      <version>3.10.0</version>
    </dependency>

    <dependency>
      <groupId>com.google.protobuf</groupId>
      <artifactId>protobuf-java-util</artifactId>
      <version>3.10.0</version>
    </dependency>
    -->

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

The transformer is _required_ - otherwise applications.conf will _not_ be filled out with defaults and you will have to define everything! See [stackoverflow](https://stackoverflow.com/questions/31011243/no-configuration-setting-found-for-key-akka-version) for more information on this.  

As for protobuf, the Akka gRPC plugin takes care of this - so we do not need to set up protobuf. In fact, there are detractions to including the usual protobuf plugin:
* It seems to generate an unnecessary duplicate classes.
* By itself, protobuf can handle a service - however its pretty rudimentary.  Many of the needed generated functionality is not done by Protobuf natively - so use Akka's version embedded in their Maven plugin. Things not generated seem to be:
   * The service class as a standalone (i.e. FruitService)
   * The XXXServiceClient class (i.e. FruitServiceClient)
   * The XXXServiceClientPowerApi class (i.e. FruitServiceClientPowerApi)
   * The XXXServiceHandlerFactory class (i.e. FruitServiceHandlerFactory)

In addition, the Akka gRPC plugin takes care of many extra things - it creates a bunch of additional helper generated classes in target/generated-sources/akka-grpc-java.  
  
Finally, you _must_ include the `includeStdTypes` tag (and set it to true) in the `akka-grpc-maven-plugin` plugin; this MUST be present IF we are including 'google/protobuf/any.proto' in the .proto file (and we are).  

## logback.xml  

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
* If you change the package name, you will obviously have to change `com.wagenseller` above.  


## application.conf  

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
* The `akka.http.server.preview.enable-http2` setting is required (although, in the configuration docs, this says its 'not fully production ready', but it is absolutely necessary).  
   * If this is _NOT_ present in the actor system config, it will not work and you will get a warning similar to this:
```
WARN akka.actor.ActorSystemImpl akka.actor.ActorSystemImpl(ActorSystem) Illegal request, responding with status '501 Not Implemented': Unsupported HTTP method: PRI   
```   
* The `remote-address-attribute` setting enables/disables the addition of a remote-address attribute in HttpRequest holding the clients (remote) IP address.  
  * This is preferred over `remote-address-header` because it cannot be confused with a real header.
  * This is not necessary, but I like to use it.  
* The `idle-timeout` setting controls the idle timeout on the server side.  
   * I am not sure there is one for the client - removing this for the client had no effect, though, so it may not be needed on the client.  
   * Minutes are set like so (for seconds, replace with an 's'): `idle-timeout = 10m`  
   * To set it to infinite, you would do this: `idle-timeout = infinite`  
* For more config options for Akka HTTP, see [here](https://doc.akka.io/docs/akka-http/current/configuration.html).  

## fruitservice.proto

```
syntax = "proto3";

option java_package = "com.wagenseller";
option java_outer_classname = "HealthServiceMsg";
option optimize_for = CODE_SIZE;

import "google/protobuf/any.proto";

service FruitService {

    // The stream of incoming FruitRequest messages are sent out as corresponding FruitResponse - from all clients to all clients.
    rpc StreamSomeFruitBetweenActors (stream FruitRequestEnvelope) returns (stream FruitResponseEnvelope) {}
}

/*
    We are restricted to the message type that is sent from the client, as well as the message type that is sent back from the server. This is a hinderance on the face of it, but
    we can use the idea of an 'envelope' as a message to get around this restriction, allowing us to send any message type that is needed.

    The 'message' below can contain _any_ type of object - this is because 'google.protobuf.Any' acts as an 'Object' object. There are some additional things you will have to do to
    pack this as a message / unpack the object, which will be shown in the code.

    The 'internalID' is not strictly required for our example, but most envelopes have an identifier of some sort so you can quickly identify certain aspects of the envelope without
    having to unpack the message.
*/
message FruitRequestEnvelope {
    string internalID = 1;
    google.protobuf.Any message = 2;
}

message FruitResponseEnvelope {
    string internalID = 1;
    google.protobuf.Any message = 2;
}

// The request message containing the user's requested fruit type.
message FruitRequest {
    string fruitType = 1;
}

// The response message containing the fruit type and its ID
message DeliveredFruit {
    string fruitInBox = 1;
}

// The response from the server, indicating what was sent was not fruit
message NonFruitRequestError {
    string message = 1;
}

// This is used to end the fruit requests
message EndFruit { }

// This represents a random class, just to show the diversity of classes that can be sent as the message in FruitRequestEnvelope
message Sword {
    string swordType = 1;
}

// This represents a random class, just to show the diversity of classes that can be sent as the message in FruitRequestEnvelope
message Veggie {
    string veggieType = 1;
}
```  

## MyMain.java  

```
package com.wagenseller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.Locale;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * This facilitates message passing between two remote actors via a gRPC stream. In this context, the gRPC stream is kept open an arbitrary amount of time so an
 * arbitrary amount of messages can be passed back and forth before the connection is terminated. If you are looking for a gRPC request that is a SINGLE request
 * and then a SINGLE response, this is not it!
 *
 * ##################
 * This is the current 'final' example of gRPC - it uses envelopes as messages, which allows for an arbitrary encapsulated message to be sent (otherwise, you would
 * have to either be limited to one sent and one receive message OR you would have to make multiple rpc objects in the protobuf file).
 * ##################
 *
 * There are two 'modes' to this application: one that acts as a server that
 * accepts a connection over gRPC, and a client that sends said connections (you can select which mode via a parameter - see the 'To run' section below).
 *
 *
 * To run:
 * Server: java -cp target/grpc-test-using-envelopes-1.0.jar com.wagenseller.MyMain -Drole=server
 * Client: java -cp target/grpc-test-using-envelopes-1.0.jar com.wagenseller.MyMain -Drole=client
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

## MaterializedDestination.java  

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

## FruitServiceImpl.java  

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
     *
     * 'FruitServiceHandlerFactory' actually calls this from the server side. Please recall that 'FruitServiceHandlerFactory' is written by Akka automatically, _provided_ you included
     * 'akka-grpc-maven-plugin' in the pom file (see the note at the top of this class)
     *
     * @param theStream
     * @return
     */
    @Override
    public Source<HealthServiceMsg.FruitResponseEnvelope, NotUsed> streamSomeFruitBetweenActors(Source<HealthServiceMsg.FruitRequestEnvelope, NotUsed> theStream) {
        // create the fruit server actor
        ActorRef newFruitActor=system.actorOf(FruitServerActor.props());

        // wire the inbound requests to the actor
        theStream.runWith(Sink.actorRef(newFruitActor, Done.done()), materializer);
        // give that actor an outbound destination for sending responses, return that as a source

        // we need to use a method here and cannot just paste the contents of the method 'materializeSourceActor' - if not, its marked as a
        // 'Source<Object, ActorRef>' and not a 'Source<FruitResponse, ActorRef>' which throws an error. I am not quite sure why this is the case, but it is.
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

    protected Source<HealthServiceMsg.FruitResponseEnvelope, ActorRef> materializeSourceActor() {
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

# Server-Specific Classes  

## Server.java  

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
        // There is an option here to add a '.exceptionally()'after the '.thenApply()' - you would be required to incorporate a Throwable, though
        // 'FruitServiceHandlerFactory' is fully generated by Akka - just remember to write FruitServiceImpl!!!
        CompletionStage<ServerBinding> futureFruit = Http.get(system)
                .newServerAt("127.0.0.1", 8080)
                .bind(FruitServiceHandlerFactory.create(fruitServiceImpl, system))
                .thenApply(binding -> this.binding=binding);

        // To stop the ENTIRE server, you would need to interact with the binding a la
        // binding.terminate(Duration.ofSeconds(3));
    }
}
```  

## FruitServerActor.java  

```  
package com.wagenseller;

import akka.Done;
import akka.actor.*;
import com.google.protobuf.Any;
import com.google.protobuf.Message;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.Arrays;
import java.util.List;
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

    // This is a listing of all possible message types we could receive as the message _inside_ the envelope
    private static final List<Class<? extends Message>> POSSIBLE_MESSAGE_TYPES = Arrays.asList(
            HealthServiceMsg.FruitRequest.class,
            HealthServiceMsg.EndFruit.class
    );

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

    private void onFruitEnvelopeRequest(HealthServiceMsg.FruitRequestEnvelope requestInEnvelope) {

        logger.info("Received an envelope in {} for id {} - processing now.", self().path().name(), requestInEnvelope.getInternalID());
        try {
            if (requestInEnvelope.getMessage().is(HealthServiceMsg.FruitRequest.class)) {

                // The below actually unpacks the message. Note you MUST know the class type here to unpack it!
                HealthServiceMsg.FruitRequest msg = requestInEnvelope.getMessage().unpack(HealthServiceMsg.FruitRequest.class);

                // process the request
                handleFruitRequest(msg);

            } else if (requestInEnvelope.getMessage().is(HealthServiceMsg.Sword.class)) {

                // The below actually unpacks the message. Note you MUST know the class type here to unpack it!
                HealthServiceMsg.Sword msg = requestInEnvelope.getMessage().unpack(HealthServiceMsg.Sword.class);

                // process the request
                handleThrownSword(msg);

            } else if (requestInEnvelope.getMessage().is(HealthServiceMsg.Veggie.class)) {

                // The below actually unpacks the message. Note you MUST know the class type here to unpack it!
                HealthServiceMsg.Veggie msg = requestInEnvelope.getMessage().unpack(HealthServiceMsg.Veggie.class);

                // process the request
                handleVeggie(msg);

            } else if (requestInEnvelope.getMessage().is(HealthServiceMsg.EndFruit.class)) {
                // COMPLETE THIS
            } else {
                logger.warn("Received unexpected class type - {}", requestInEnvelope.getMessage().getClass());
            }
        } catch (com.google.protobuf.InvalidProtocolBufferException e) {

        }


        // Instead of the above 'if' tree, you could simply unpack the message and then re-send it to ourself. If you built the actual class in the createReceive() it would be
        // handled like a normal message to this actor. That said, this is a bit inefficient as it has to exit this actor and then be re-processed by the mailbox, so its best
        // to do it directly as above
        /*
        for (Class<? extends Message> clazz : POSSIBLE_MESSAGE_TYPES) {
            if (requestInEnvelope.getMessage().is(clazz)) {
                try {
                    // simply send the actual message to ourself as a tell - you can put the associated class in the createReceive() here and it would be handled like a message
                    self().tell(requestInEnvelope.getMessage().unpack(clazz), self());
                } catch (com.google.protobuf.InvalidProtocolBufferException e) {
                    logger.error("Attempted to unpack a [{}] message but it could not be done!", clazz);
                }
            }
        }
        */
    }

    private void handleThrownSword(HealthServiceMsg.Sword request) {

        String sword = request.getSwordType();
        logger.info("The client threw a {} sword at {} - returning the favor.", sword, self().path().name());

        HealthServiceMsg.NonFruitRequestError response = HealthServiceMsg.NonFruitRequestError.newBuilder()
                .setMessage("Really, you threw a " + sword + " at me? This isn't Fruit Ninja, fool.")
                .build();

        // Now build the envelope
        HealthServiceMsg.FruitResponseEnvelope envelope = HealthServiceMsg.FruitResponseEnvelope.newBuilder()
                .setMessage(Any.pack(response)).build();

        //send the message to the client!
        outboundActor.tell(envelope, getSelf());

    }

    private void handleVeggie(HealthServiceMsg.Veggie request) {

        String veggie = request.getVeggieType();
        logger.info("The client requested a veggie {} from our fruit server - letting the client know we cannot service that request.", veggie, self().path().name());

        HealthServiceMsg.NonFruitRequestError response = HealthServiceMsg.NonFruitRequestError.newBuilder()
                .setMessage("Really, you requested a " + veggie + " but we do NOT do veggies here!")
                .build();

        // Now build the envelope
        HealthServiceMsg.FruitResponseEnvelope envelope = HealthServiceMsg.FruitResponseEnvelope.newBuilder()
                .setMessage(Any.pack(response)).build();

        //send the message to the client!
        outboundActor.tell(envelope, getSelf());

    }

    private void handleFruitRequest(HealthServiceMsg.FruitRequest request) {

        fruitType = request.getFruitType();
        logger.info("Received new fruit request in [{}] for fruit [{}]", self().path().name(), fruitType);

        // we want to start a timer that will send a piece of fruit back to the client in 5 second intervals
        // only start the timer if its not already started
        if (getTimers().isTimerActive(SEND_A_PIECE_OF_FRUIT) == false) {
            // start the timer which will send a piece of fruit at regular intervals
            getTimers().startTimerAtFixedRate(SEND_A_PIECE_OF_FRUIT, new SendAPieceOfFruit(), java.time.Duration.ofMillis(5000));
        }

    }

    private void handleSendAPieceOfFruit(SendAPieceOfFruit msg) {

        // if we have thrown 5 pieces of fruit, end the fruit throwing
        if (individualFruitNumber >=5) {
            logger.info("All out of fruit type [{}]! Notifying client we are done", fruitType, individualFruitNumber);

            outboundActor.tell(Done.getInstance(), getSelf());
            //outboundActor.tell(Done.done(), getSelf());

        } else {
            logger.info("Throwing fruit type [{}] (number [{}]) to the client!", fruitType, individualFruitNumber);

            // Build the DeliveredFruit, which will be the message
            HealthServiceMsg.DeliveredFruit fruitResponse = HealthServiceMsg.DeliveredFruit.newBuilder()
                    .setFruitInBox(fruitType.toUpperCase(Locale.ROOT) + ++individualFruitNumber)
                    .build();

            // Now build the envelope
            HealthServiceMsg.FruitResponseEnvelope envelope = HealthServiceMsg.FruitResponseEnvelope.newBuilder()
                    .setMessage(Any.pack(fruitResponse)).build();

            //send the message to the client!
            outboundActor.tell(envelope, getSelf());
        }
    }

    /**
     * Note none of the internal messages are mentioned here - they are handled in onFruitEnvelopeRequest.
     *
     * @return
     */
    public Receive createReceive() {
        return receiveBuilder()
                .match(HealthServiceMsg.FruitRequestEnvelope.class, this::onFruitEnvelopeRequest)
                .match(MaterializedDestination.class, this::setOutboundActor)
                .match(SendAPieceOfFruit.class, this::handleSendAPieceOfFruit)
                .match(Status.Failure.class, this::handleStatusFailure)
                .match(Done.class, this::handleDone)
                .build();
    }
}
```  

# Client-Specific Classes  

## Client.java  

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

## FruitClientActor.java  

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
import com.google.protobuf.Any;
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
 * 3. FruitClientActor binds itself to the stream, which accepts messages from the server.
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

    // This timer controls the throwing of a sword - this is to demonstrate the diversity of message types
    // note that this uses an Akka timer
    private static final class ThrowSword {}
    private static Object THROW_SWORD = "THROW_SWORD";

    // This timer controls the throwing of a sword - this is to demonstrate the diversity of message types
    // note that this uses an Akka timer
    private static final class RequestVeggie {}
    private static Object REQUEST_VEGGIE = "REQUEST_VEGGIE";

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
        // Please recall that 'FruitServiceClient' is written by Akka automatically, _provided_ you included 'akka-grpc-maven-plugin' in the pom file (see the pom file for more details)
        client = FruitServiceClient.create(settings, getContext().getSystem());

        // This gets the request source by materializing a source actor
        Source<HealthServiceMsg.FruitRequestEnvelope, NotUsed> requests = materializeSourceActor();


        // The above materializes an actor that will accept the incoming messages from a stream - more specifically, its an actor that is _also_ a
        // stream source.
        // Connect this materialized source actor to the client's responses
        Source<HealthServiceMsg.FruitResponseEnvelope, NotUsed> responses = client.streamSomeFruitBetweenActors(requests);

        // have these responses mapped back to a _specific_ method - in our case, when we get a message, we will send that message to the
        // method 'handleResponseFromServer'
        // The .runWith() will forward on the message to this actor (i.e. FruitClientActor)
        responses.map(this::handleResponseFromServer)
                .runWith(Sink.actorRef(getSelf(), Done.done()), materializer);

        // This starts a timer to throw a sword at the server once after 12 seconds
        // After 12 seconds, this will send a 'ThrowSword' message to ourself - from there we need to do something
        getTimers().startSingleTimer(THROW_SWORD, new ThrowSword(), java.time.Duration.ofSeconds(12));

        // This starts a timer to request a veggie after 17 seconds
        // After 17 seconds, this will send a 'RequestVeggie' message to ourself - from there we need to do something
        getTimers().startSingleTimer(REQUEST_VEGGIE, new RequestVeggie(), java.time.Duration.ofSeconds(17));
    }

    public void postStop() {
        logger.info("Stopping client actor for fruit type [{}]!", fruitType);
    }

    /**
     * This acts as a landing for responses from the server. Really, this could be eliminated and we could simply use the method 'handleFruitResponseEnvelope()'
     * in the 'responses.map().runWith()' above, but this is to showcase how this could work if you have need for multiple response types.
     *
     * This will extract the message and then return JUST the message - not the envelope - back to this actor (via the rinWith() above) for further processing.
     *
     * This returns a value, and that value is sent back to this actor
     *
     * @param requestInEnvelope
     * @return
     */
    private Message handleResponseFromServer(HealthServiceMsg.FruitResponseEnvelope requestInEnvelope) {
        logger.info("Handling response envelope from server for fruit type {} with ID {}.", fruitType, requestInEnvelope.getInternalID());

        Message msg = null;
        try {
            if (requestInEnvelope.getMessage().is(HealthServiceMsg.DeliveredFruit.class)) {

                // The below actually unpacks the message. Note you MUST know the class type here to unpack it!
                msg = requestInEnvelope.getMessage().unpack(HealthServiceMsg.DeliveredFruit.class);

            } else if (requestInEnvelope.getMessage().is(HealthServiceMsg.EndFruit.class)) {
                msg = requestInEnvelope.getMessage().unpack(HealthServiceMsg.EndFruit.class);
            } else if (requestInEnvelope.getMessage().is(HealthServiceMsg.NonFruitRequestError.class)) {
                msg = requestInEnvelope.getMessage().unpack(HealthServiceMsg.NonFruitRequestError.class);
            } else {
                logger.warn("Received unexpected class type - {}", requestInEnvelope.getMessage().getClass());
            }
        } catch (com.google.protobuf.InvalidProtocolBufferException e) {

        }
        return msg;
    }

    /**
     * Materializes the source actor. This also calls methods that link the materialized actor to this actor (FruitClientActor) so we can interact with it.
     *
     * @return
     */
    protected Source<HealthServiceMsg.FruitRequestEnvelope, NotUsed> materializeSourceActor() {

        // actually create the materialized actor
        Source<HealthServiceMsg.FruitRequestEnvelope, ActorRef> actorRefSource =
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
        Source<HealthServiceMsg.FruitRequestEnvelope, NotUsed> materializeActor = actorRefSource.mapMaterializedValue(captureMaterializedActor(self(), logger));

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
        HealthServiceMsg.FruitRequest newFruit = HealthServiceMsg.FruitRequest.newBuilder().setFruitType(fruitType).build();
        sendMessageToServer(newFruit);

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

    /**
     * This sends a message to the server. The actual message is arbitrary, but it will be wrapped in the appropriate envelope.
     *
     * @param msg
     */
    private void sendMessageToServer(Message msg) {

        // We will use a builder for this. Start building the envelope using what we can fill out now
        HealthServiceMsg.FruitRequestEnvelope.Builder envelopeBuilder = HealthServiceMsg.FruitRequestEnvelope.newBuilder().setInternalID("12345");

        // To add the message, the object MUST be of type T and NOT Object - so if we did not declare msg as an Object above we would be fine, but since we did, we need to
        // explicitly typecast the object
        if (msg instanceof HealthServiceMsg.FruitRequest) {
            envelopeBuilder = envelopeBuilder.setMessage(Any.pack((HealthServiceMsg.FruitRequest) msg));
        } else if (msg instanceof HealthServiceMsg.Sword) {
            envelopeBuilder = envelopeBuilder.setMessage(Any.pack((HealthServiceMsg.Sword) msg));
        } else if (msg instanceof HealthServiceMsg.Veggie) {
            envelopeBuilder = envelopeBuilder.setMessage(Any.pack((HealthServiceMsg.Veggie) msg));
        } else if (msg instanceof HealthServiceMsg.EndFruit) {
            envelopeBuilder = envelopeBuilder.setMessage(Any.pack((HealthServiceMsg.EndFruit) msg));
        }

        // finish building the envelope
        HealthServiceMsg.FruitRequestEnvelope envelope = envelopeBuilder.build();

        // The envelope goes to the server over the materialized actor
        outboundActor.tell(envelope, self());
    }

    /**
     * This should never be used, but I put it in here for posterity.
     *
     * @param requestInEnvelope
     */
    private void handleFruitResponseEnvelope(HealthServiceMsg.FruitResponseEnvelope requestInEnvelope) {

        logger.info("Received an envelope in {} for id {} as a direct message (which should not happen) - processing now.", self().path().name(), requestInEnvelope.getInternalID());

        Message message = handleResponseFromServer(requestInEnvelope);

        if (message instanceof HealthServiceMsg.DeliveredFruit) {
            handleDeliveredFruit((HealthServiceMsg.DeliveredFruit) message);
        } else if (message instanceof HealthServiceMsg.EndFruit) {

        } else if (message instanceof HealthServiceMsg.NonFruitRequestError) {
            handleNonFruitRequestError((HealthServiceMsg.NonFruitRequestError) message);
        }
    }

    private void handleNonFruitRequestError(HealthServiceMsg.NonFruitRequestError nonFruitRequestError) {
        logger.info("Received non-fruit response from server: {}", nonFruitRequestError.getMessage());
    }

    private void handleDeliveredFruit(HealthServiceMsg.DeliveredFruit msg) {

        logger.info("Eating individual piece of fruit [{}]!", msg.getFruitInBox());

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
     * This is kicked off by a timer that is set above in the code. When the timer goes off, a ThrowSword object is sent to ourself, so we end up having to handle that.
     * What we will now do is throw a sword at the server.
     *
     * @param msg
     */
    private void handleThrowSword(ThrowSword msg) {

        HealthServiceMsg.Sword throwSword = HealthServiceMsg.Sword.newBuilder().setSwordType("machete").build();

        // Throw the sword at the server! This called method will wrap the message in an envelope and then send it
        sendMessageToServer(throwSword);
    }

    /**
     * This is kicked off by a timer that is set above in the code. When the timer goes off, a RequestVeggie object is sent to ourself, so we end up having to handle that.
     * What we will now do is request a veggie from the server.
     *
     * @param msg
     */
    private void handleRequestVeggie(RequestVeggie msg) {

        HealthServiceMsg.Veggie requestVeggie = HealthServiceMsg.Veggie.newBuilder().setVeggieType("cabbage").build();

        // Request the veggie from the server!
        sendMessageToServer(requestVeggie);
    }

    public Receive createReceive() {
        return receiveBuilder()
                .match(HealthServiceMsg.FruitResponseEnvelope.class, this::handleFruitResponseEnvelope) // will probably not be used but here for posterity
                .match(HealthServiceMsg.DeliveredFruit.class, this::handleDeliveredFruit) // will be used due to how the flow works (runsWith() is used - see above)
                .match(HealthServiceMsg.NonFruitRequestError.class, this::handleNonFruitRequestError) // will be used due to how the flow works (runsWith() is used - see above)
                .match(MaterializedDestination.class, this::setOutboundActor)
                .match(Status.Failure.class, this::handleStatusFailure)
                .match(ThrowSword.class, this::handleThrowSword)
                .match(RequestVeggie.class, this::handleRequestVeggie)
                .match(Done.class, this::handleDone)
                .build();
    }
}
```  