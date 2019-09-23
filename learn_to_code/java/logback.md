# Logging In Java with Logback

# What Is Logback?

> The official Logback manual can be found [here](https://logback.qos.ch/manual/index.html). There is a nice introduction on [mograblog](http://www.mograblog.com/2013/03/slf4j-with-logback-in-maven-project.html), and [memorynotfound](https://memorynotfound.com/logback-logback-xml-configuration-example/) has a nice example of logback.xml. Finally, [igorski.co](https://igorski.co/java/logging/log-hierarchy-logback/) gives an example of hierarchy.

<font color="green">Logback</font> is a logging system in Java that is intended to be a successor to the log4j project (designed by Ceki Gülcü, the same guy who made log4j). It is purported to have a smaller footprint than other logging systems.

There are 3 modules in <font color="green">Logback</font>:
* <font color="purple">logback-core</font>
 * lays the groundwork for all 3 modules
* <font color="purple">logback-classic</font>
 * extends <font color="purple">logback-core</font>.
 * Implements the SLF4J API, so you can switch between log4j and java.util.logging as well
 * _This is the most common of the 3.
* <font color="purple">logback-access</font>
 * This provides HTTP-access log functionality - see [here](https://logback.qos.ch/access.html) for that documentation.




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
* [logback.xml](learn_to_code/java/logback?id=logbackxml)
* logback-test.xml
* logback.groovy - I believe this is much like [logback.xml](learn_to_code/java/logback?id=logbackxml), but is used for the Apache Groovy Language instead of Java. Currently I will not be covering Groovy, but I may in the future.


## Location of Files in Maven

In order for these configuration files to be present in [the compiled jar file that is created by Maven](learn_to_code/java/maven?id=the-compiled-jar-file), you must put these files in the [resources folder](learn_to_code/java/maven?id=resources-in-maven) of the project.

# Levels of Severity

All loggers have a severity level (it may be assigned, but if its not, its always inherited by its parent) - these levels are, in order from least to most severe, are:
1. `TRACE`
2. `DEBUG`
3. `INFO`
4. `WARN`
5. `ERROR`




# Logback.xml

<font color="green">Logback.xml</font> is a configuration file that details information about the file (things like log location, log name, etc). This file is specifically used by Java.

!> This file defines where the logs should be stored; if the path does not exist, Logback will create the path and file _provided_ the user running the Java application has [the rights](ubuntu/linux_notes?id=changing-file-permissions) to do so.

## Example Logback.xml

```
<configuration scan="false" debug="true">
	<conversionRule conversionWord="traceToken" converterClass="kamon.trace.logging.LogbackTraceTokenConverter"/>
	<statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />
	
	<appender name="waglog" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<prudent>true</prudent>
		<file>/home/manifest/logs/waglog.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>/home/manifest/logs/waglog.log-%d{yyyy.MM.dd,UTC}</fileNamePattern>
			<maxHistory>15</maxHistory>
		</rollingPolicy>
		<encoder>
			<charset>UTF-8</charset>
			<pattern>%d{yyyy-MM-dd HH:mm:ss,SSS} %level [%X{sourceThread}] %logger{36} %X{akkaSource} %msg%n</pattern>
		</encoder>
	</appender>
	
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<charset>UTF-8</charset>
			<pattern>%d{yyyy-MM-dd HH:mm:ss,SSS} %-5level [%X{sourceThread}] %logger{36} %X{akkaSource} %msg%n</pattern>
		</encoder>
	</appender>
	
	<logger name="waglog" level="DEBUG"/>
	<logger name="io.netty.handler.codec.http" level="WARN"/>

	<root level="DEBUG">
		<appender-ref ref="waglog" />
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```

# Example Project

!> This requires [logback.xml](learn_to_code/java/logback?id=logbackxml) to be accessible to your code (usually it sits at the root level of your jar file).

```
package com.wagenseller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;


public class LogbackExample {

    private static final Logger logger   = LoggerFactory.getLogger("waglog");
    private static final Logger sec_logger   = LoggerFactory.getLogger("Second Log");

    public static void main( String[] args )
    {
        logger.info("TEST-OOOOOOOOOO");
        sec_logger.info("second stuff is here!");
        logger.debug("This is a debug message");
        logger.warn("This is a WARNING");
        sec_logger.error("CRITICAL ERROR!");
        
        System.out.println( "Hello World!" );
    }
}
```

This will print the following:
```
2019-09-12 01:56:49 INFO [] waglog  TEST-OOOOOOOOOO
2019-09-12 01:56:49 INFO [] Second Log  second stuff is here!
2019-09-12 01:56:49 DEBUG [] waglog  This is a debug message
2019-09-12 01:56:49 WARN [] waglog  This is a WARNING
2019-09-12 01:56:49 ERROR [] Second Log  CRITICAL ERROR!
```

