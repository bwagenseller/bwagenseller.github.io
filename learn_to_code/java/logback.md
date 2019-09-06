# Logging In Java with Logback

# What Is Logback?

> The official Logback manual can be found [here](https://logback.qos.ch/manual/index.html). There is a nice introduction on [mograblog](http://www.mograblog.com/2013/03/slf4j-with-logback-in-maven-project.html), and [memorynotfound](https://memorynotfound.com/logback-logback-xml-configuration-example/) has a nice example of logback.xml.

<font color="purple">Logback</font> is a logging system in Java that is intended to be a successor to the log4j project (designed by Ceki Gülcü, the same guy who made log4j). It is purported to have a smaller footprint than other logging systems.

# Requirements In Project

## Standalone Jars

If you simply want to download the jar files you will need:
* slf4j-api.jar
* logback-core.jar
* logback-classic.jar

## As Maven Dependency

Its possible to include logback in Maven - to do so, you will have to add a few [dependencies](learn_to_code/java/maven?id=adding-dependencies-to-maven) to the [pom.xml](learn_to_code/java/maven?id=pomxml) file.

To add Logback to a Maven project, simply add these dependencies:
```
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>1.2.3</version>
			<scope>runtime</scope>
		</dependency>		
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>1.7.28</version>
		</dependency>		
```
* You are free to use a different version - to see the different versions, search for `logback-classic` [here](https://mvnrepository.com/artifact/ch.qos.logback/logback-classic) and `slf4j-api` [here](https://mvnrepository.com/artifact/org.slf4j/slf4j-api).

Note that the official documentation says to add `logback-core` as well, but from what I can tell, `logback-classic` contains `logback-core` - so I do not expressly include it. That said, if you wish to include it its:
```
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
			<version>1.2.3</version>
		</dependency>	
```
* You are free to use a different version - to see the different versions, search for `logback-core` [here](https://mvnrepository.com/artifact/ch.qos.logback/logback-core).

Finally, although `slf4j-api` is required, you can avoid it if you are using [Akka's](learn_to_code/java/akka/) implementation of `akka-slf4j` - if you are, you can use this instead of the dependency for `slf4j-api` (this is recommended when using Akka):
```
		<dependency>
			<groupId>com.typesafe.akka</groupId>
			<artifactId>akka-slf4j_2.11</artifactId>
			<version>2.5.19</version>
		</dependency>
```
* Note this is a bit different than most artifactIDs; you can see more of these [here](https://mvnrepository.com/artifact/com.typesafe.akka/akka-slf4j).

# Configuration Files

Logback uses configuration files that are meant to sit at the root of your [compiled jar file](learn_to_code/java/java_basics?id=jar-files) - if they do not exist, Logback will still work, but you will lose some powerful functionality of the tool.

These files are:
* logback.xml


## Location of Files in Maven

In order for these configuration files to be present in [the compiled jar file that is created by Maven](learn_to_code/java/maven?id=the-compiled-jar-file), you must put these files in the [resources folder](learn_to_code/java/maven?id=resources-in-maven) of the project.


# Logback.xml

