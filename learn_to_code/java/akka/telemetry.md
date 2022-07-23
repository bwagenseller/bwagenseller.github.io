# Lightbend Telemetry  

# Sources For This Section  

I used he following resources to take notes on this section:  
* The official training course on [Lightbend Telemetry / Cinnamon](https://academy.lightbend.com/courses/course-v1:lightbend+LTM-Installation+v1/about).  
   * All of their courses are listed [here](https://academy.lightbend.com/courses), if that interests you.  

# Basics  

## Purpose of Telemetry  

The main purpose of <font color="green">Telemetry</font> is to monitor Akka distributed systems; distributed systems are hard to troubleshoot as there are many moving parts - <font color="green">Telemetry</font> helps with that. <font color="green">Cinnamon</font> is the official name for Lightbend's <font color="purple">Telemetry</font>

In addition, <font color="green">Cinnamon</font> can help us:  
* Correlate problems across a distributed system.  
* Detect an issue before it happens.  
* See how configuration changes affect the system.  

You can do all of this yourself by writing code, but its a large effort to do so - so why not take advantage of an existing, unobtrusive product?  

## Getting Your Token  

Unlike most Akka dependencies, Lightbend Telemetry _requires_ you to have credentials; I am fairly certain (but not fully certain) a free account will work for training purposes, but it will not work for production (you will need to contact Lightbend if you wish to use this in production). To get your credentials you will have to [go to the Lightbend Customer Portal / credentials site](https://www.lightbend.com/account/lightbend-platform/credentials); this page _should_ have a personalized url for you (highlighted in yellow). The credentials are entered in different ways, depending on if you are using sbt, Maven, or Gradle (go to the credentials site for more information). For maven, this is a repository in the pom.xml file which will look like so:  
```
<repository>
	<id>lightbend-commercial</id>
	<name>Lightbend Commercial</name>
	<url>https://repo.lightbend.com/pass/YOUR_TOKEN_HERE/commercial-releases</url>
</repository>
```  
* The `YOUR_TOKEN_HERE` should be tailored specifically for you, personally.  
* You should be able to just copy/paste this right into your sbt, Maven, or Gradle file.  

## Adding Cinnamon to Project  

> The official instructions are [here](https://developer.lightbend.com/docs/telemetry/current/setup/setup.html), which include not only Maven setup but sbt and Gradle as well.  

The setup of <font color="green">Cinnamon</font> is mostly in your project setup; for Maven, thats your pom.xml file. Here is the bare-bones setup for Cinnamon to run in Maven:  
```
<project>
  <modelVersion>4.0.0</modelVersion>

  <groupId>test.app</groupId>
  <artifactId>app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <distributionManagement>
    <repository>
      <id>lightbend-commercial</id>
      <name>Lightbend Commercial</name>
	  <!--  This contains a personal token - if yo udo not do this, this will fail. Get your token at:  https://www.lightbend.com/account/lightbend-platform/credentials  -->
      <url>https://repo.lightbend.com/pass/YOUR_PERSONAL_TOKEN_HERE/commercial-releases</url>
    </repository>
  </distributionManagement>  

  <dependencies>
    <!-- Use Coda Hale Metrics -->
    <dependency>
      <groupId>com.lightbend.cinnamon</groupId>
      <artifactId>cinnamon-chmetrics</artifactId>
      <version>2.16.3</version>
    </dependency>
    <!-- Use Akka instrumentation -->
    <dependency>
      <groupId>com.lightbend.cinnamon</groupId>
      <artifactId>cinnamon-akka_2.12</artifactId>
      <version>2.16.3</version>
    </dependency>
    <dependency>
      <groupId>com.lightbend.cinnamon</groupId>
      <artifactId>cinnamon-akka-typed_2.12</artifactId>
      <version>2.16.3</version>
    </dependency>
    <dependency>
      <groupId>com.lightbend.cinnamon</groupId>
      <artifactId>cinnamon-akka-persistence_2.12</artifactId>
      <version>2.16.3</version>
    </dependency>
    <dependency>
      <groupId>com.lightbend.cinnamon</groupId>
      <artifactId>cinnamon-akka-stream_2.12</artifactId>
      <version>2.16.3</version>
    </dependency>
    <dependency>
      <groupId>com.lightbend.cinnamon</groupId>
      <artifactId>cinnamon-akka-projection_2.12</artifactId>
      <version>2.16.3</version>
    </dependency>
    <!-- Use Akka HTTP instrumentation -->
    <dependency>
      <groupId>com.lightbend.cinnamon</groupId>
      <artifactId>cinnamon-akka-http_2.12</artifactId>
      <version>2.16.3</version>
    </dependency>
    <!-- Use Akka gRPC instrumentation -->
    <dependency>
      <groupId>com.lightbend.cinnamon</groupId>
      <artifactId>cinnamon-akka-grpc_2.12</artifactId>
      <version>2.16.3</version>
    </dependency>
    <dependency>
      <groupId>com.typesafe.akka</groupId>
      <artifactId>akka-actor_2.12</artifactId>
      <version>2.5.32</version>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
        </configuration>
      </plugin>
	  
	  <!-- This plugin downloads the cinnamon-agent.jar file, which needs to run alongside whatever project you build -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
        <version>3.1.2</version>
        <executions>
          <execution>
            <id>copy</id>
            <phase>compile</phase>
            <goals>
              <goal>copy</goal>
            </goals>
            <configuration>
              <artifactItems>
                <artifactItem>
                  <groupId>com.lightbend.cinnamon</groupId>
                  <artifactId>cinnamon-agent</artifactId>
                  <version>2.16.3</version>
                  <overWrite>true</overWrite>
                  <destFileName>cinnamon-agent.jar</destFileName>
                </artifactItem>
              </artifactItems>
            </configuration>
          </execution>
        </executions>
      </plugin>
	  
	  <!-- This is required in order to add the agent to the exec plugin for the `exec:exec` task -->
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>3.0.0</version>
        <configuration>
          <executable>java</executable>
          <arguments>
            <argument>-classpath</argument>
            <classpath/>
            <argument>-javaagent:${project.build.directory}/dependency/cinnamon-agent.jar</argument>
			<!-- Commented out but you can add more arguments
            <argument>... your other arguments here ...</argument>
			-->
          </arguments>
        </configuration>
      </plugin>
	  
	  <!-- This plugin is for testing purposes; if you want Cinnamon active for tests this is required.  -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.22.2</version>
        <configuration>
          <argLine>-javaagent:${project.build.directory}/dependency/cinnamon-agent.jar</argLine>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```
* You must replace `YOUR_PERSONAL_TOKEN_HERE` with [your token](learn_to_code/java/akka/telemetry?id=getting-your-token).  
* The plugin `maven-dependency-plugin` downloads the `cinnamon-agent.jar` file, which needs to run alongside whatever project you build.  
   * Without this jar file, <font color="green">Cinnamon</font> has no way of actually running.  
* The `exec-maven-plugin` is required in order to add the agent to the exec plugin for the `exec:exec` task.  
* The `maven-surefire-plugin` is for testing using the agent.
* There is also a setup for `docker-maven-plugin` if you want to check that out [here](https://developer.lightbend.com/docs/telemetry/current/setup/cinnamon-agent-maven.html#adding-the-agent).  
* `cinnamon-chmetrics` is the backend plugin.  

## Running Cinnamon Agent  

To run the Cinnamon Agent:  
```
java -javaagent:cinnamon-agent.jar WHATEVER_ELSE_YOU_WANT_TO_RUN
```
* The `WHATEVER_ELSE_YOU_WANT_TO_RUN` is the entirety of the rest of your line, including classpaths, packages, options, etc.  
* The location of the `cinnamon-agent.jar` may not be quite the above, but you will need to specify it.  
* If `-javaagent` is _not_ first it probably will not work.  

If you see lines that say `[Cinnamon]` in the logs at the very beginning of startup, its working; examples:
```
[INFO] [05/27/2022 11:27:48.290] [main-1] [Cinnamon] Agent found Akka Actor version: 2.6.13
[INFO] [05/27/2022 11:27:48.290] [main-1] [Cinnamon] Agent found Scala version: 2.13.3
[INFO] [05/27/2022 11:27:48.304] [main-1] [Cinnamon] Agent found Akka version: 2.6.13
[INFO] [05/27/2022 11:27:48.313] [main-1] [Cinnamon] Agent found Scala Futures version: 2.13.3
```  
* You will also see things like `cinnamon.akka.http.CinnamonAkkaHttpInstrumentation`, `cinnamon.akka.http.HttpConfiguration$`, `cinnamon.akka.stream.CinnamonAkkaStreamInstrumentation` etc if Cinnamon starts correctly.  

# Cinnamon Components  

There are three components to <font color="green">Cinnamon</font>:  
* Agent - This creates the actual metrics.  
   * This is `cinnamon-agent.jar`.  
* Instrumentation - Specifies how to instrument Lightbend features.  
   * These are the dependencies in your Maven pom.xml, sbt setup, etc.  
* Backend Plugin - Enables metrics reporting to external backend systems  
   * For example, `cinnamon-chmetrics`.  

## Java Agent  

The <font color="green">Java Agent</font>:
* is an app that runs with your application.  
* intercepts the Bytecode of your application before it gets to the JVM, altering classes so they can be monitored by <font color="purple">Cinnamon</font>.  
   * The Agent add or alters fields and methods to your objects so they can produce metrics for <font color="purple">Cinnamon</font>.  
* must be added, usually in your pom.xml file






