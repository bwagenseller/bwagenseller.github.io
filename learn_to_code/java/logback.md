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
* [logback.xml](learn_to_code/java/logback?id=logbackxml) - This is the primary file you will be using.  
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

There are multiple times you will be required to use one of these levels; only that level _or_ a more severe level will show up in the logs based on whatever you choose. For example, if you choose `INFO`, only logs that are of level`INFO`, `WARN`, or `ERROR` will be printed to the logs - `TRACE` and `DEBUG` will _not_ be printed.  

# Logback.xml

<font color="green">Logback.xml</font> is a configuration file that details information about the file (things like log location, log name, etc). This file is specifically used by Java. Remember, if using Maven, you must put this file in the [resources folder](learn_to_code/java/maven?id=resources-in-maven) of the project; otherwise, put it in the same directory as the jar file itself.  

!> This file defines where the logs should be stored; if the path does not exist, Logback will create the path and file _provided_ the user running the Java application has [the rights](operating_systems/ubuntu/linux_notes?id=changing-file-permissions) to do so.

## Example Logback.xml

```
<configuration scan="false" debug="true">
	<conversionRule conversionWord="traceToken" converterClass="kamon.trace.logging.LogbackTraceTokenConverter"/>
	<statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />
	<property file="/etc/log.properties" />
	
	<appender name="waglogAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>/home/someuser/logs/waglog.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>/home/someuser/logs/waglog.log-%d{yyyy.MM.dd,UTC}</fileNamePattern>
			<maxHistory>15</maxHistory>
		</rollingPolicy>
		<encoder>
			<charset>UTF-8</charset>
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS,UTC} %highlight(%level) [%thread] %cyan(%logger{36}) %magenta(%X{akkaSource}) %msg%n</pattern>
		</encoder>
	</appender>
	
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<charset>UTF-8</charset>
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS,UTC} %highlight(%level) [%thread] %cyan(%logger{36}) %magenta(%X{akkaSource}) %msg%n</pattern>
		</encoder>
	</appender>	
	
	<logger name="waglog" level="${LOGLEVEL_WAG:-INFO}" additivity="false">
        <appender-ref ref="waglogAppender"/>
		<appender-ref ref="STDOUT"/>
    </logger>
	
	<logger name="io.netty.handler.codec.http" level="${LOGLEVEL_NETTY:-WARN}" additivity="false">
        <appender-ref ref="STDOUT"/>
    </logger>	

	<root level="INFO">
		<appender-ref ref="waglogAppender" />
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```

# Properties  

!> The <font color="green">properties</font> file is _just_ for variables - do not put any XML in here!  

The <font color="green">properties</font> file is a place for variables to be defined - this is usually for [log levels](learn_to_code/java/logback?id=levels-of-severity) but can sometimes be used for other things as well. An example of the actual file [is here](learn_to_code/java/logback?id=example-property-file), but the file must also be defined in logback.xml - in [my example](learn_to_code/java/logback?id=example-logbackxml) its defined like this:  

```
<property file="/etc/log.properties" />
```  

There are two variables defined in my example: `LOGLEVEL_NETTY` (which has a log level of `WARN`) and `LOGLEVEL_WAG` (which has a log level of `DEBUG`).  

## Referencing Variables In Properties  

In order to reference them in [logback.xml](learn_to_code/java/logback?id=logbackxml) you can do so with the following notation:  
```
${LOGLEVEL_NETTY:-WARN}
```  

Note the `:-WARN` is a default: if the variable cannot be found for whatever reason, the default of `WARN` is used instead.  

> The <font color="green">properties</font> file does not have to exist - if it doesnt, and thus the variable cannot be found, the default value for the variable is taken. That said, the <font color="green">properties</font> file can be placed on a server and each server can have different log settings if desired.  

## Example Property File  

This file stores the [properties](learn_to_code/java/logback?id=properties) (i.e. variables) in logback.xml.  

```
LOGLEVEL_NETTY=WARN
LOGLEVEL_WAG=DEBUG
```

# Appenders

> More on <font color="green">Appenders</font> can be found [here](http://logback.qos.ch/manual/appenders.html).  

An <font color="green">Appender</font> describes how the data will be moved to an output; it defines things like file names, locations, patterns in the log, etc.  Technically an appender must implement the `ch.qos.logback.core.Appender` interface - so there can be many types of <font color="green">appenders</font> - but in practice there are three main ones:  
* [ConsoleAppender](learn_to_code/java/logback?id=consoleappender) - This is what is used on what is seen on the console / `System.out`.  It is what is seen in tools that use `syslog` (such as [journalctl](operating_systems/ubuntu/package_operations/journalctl).  
* <font color="purple">FileAppender</font> - The basic pick that appends logs to a file (note - I never use this one and simply use [RollingFileAppender](learn_to_code/java/logback?id=rollingfileappender)).  
* [RollingFileAppender](learn_to_code/java/logback?id=rollingfileappender) - A more advanced version of <font color="purple">FileAppender</font> that rolls the log over after specific criteria is met.  

> There can be multiple <font color="green">Appenders</font> in a [logback.xml](learn_to_code/java/logback?id=logbackxml) file; you can have them print to multiple logs using different settings.  

## ConsoleAppender  

> For more on the <font color="green">ConsoleAppender</font> see [here](http://logback.qos.ch/manual/appenders.html#ConsoleAppender).  

This is what is used when you wish to format what is seen on the console / `System.out`.  It is what is seen in tools that use `syslog` (such as [journalctl](operating_systems/ubuntu/package_operations/journalctl)).  An example of a <font color="green">ConsoleAppender</font> can be found in the [example](learn_to_code/java/logback?id=example-logbackxml):  
 ```
 	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<charset>UTF-8</charset>
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS,UTC} %highlight(%level) [%thread] %cyan(%logger{36}) %magenta(%X{akkaSource}) %msg%n</pattern>
		</encoder>
	</appender>	
 ```
 
 The `class="ch.qos.logback.core.ConsoleAppender"` defines this as a <font color="green">ConsoleAppender</font> and we give it the name `STDOUT`.  We set an appropriate [encoding pattern](learn_to_code/java/logback?id=encoders) and define `charset`. We have now configured the output to `System.out`!  
 
> Its also a good idea to create a file appender that will print to a file as well.  

## RollingFileAppender  

> For more on the regular <font color="green">FileAppender</font> see [here](http://logback.qos.ch/manual/appenders.html#FileAppender), and for more on the <font color="green">RollingFileAppender</font> see [here](http://logback.qos.ch/manual/appenders.html#RollingFileAppender).  Note I do not cover the regular <font color="green">FileAppender</font> - so check that out if you need it - but the more popular <font color="green">RollingFileAppender</font> is covered here.  

In many cases, an external log can get unmanageably large quickly, and some sort of system would have to be deployed to manage the log file (i.e. you would have to write code to warehouse the current log and then start a new log); <font color="green">RollingFileAppender</font> give you this ability with ease. The [example](learn_to_code/java/logback?id=example-logbackxml) we give is:  
```
    <appender name="waglogAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>/home/someuser/logs/waglog.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>/home/someuser/logs/waglog.log-%d{yyyy.MM.dd,UTC}</fileNamePattern>
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS,UTC} %highlight(%level) [%thread] %cyan(%logger{36}) %magenta(%X{akkaSource}) %msg%n</pattern>
        </encoder>
    </appender>
```  

The basics of the above:  
* The `class="ch.qos.logback.core.rolling.RollingFileAppender"` denotes this as a <font color="green">RollingFileAppender</font>.  
* `<file>/home/someuser/logs/waglog.log</file>` denotes the initial location of where the logs go.  
* The [encoder](learn_to_code/java/logback?id=encoders) is also listed.  

The keystone of <font color="green">RollingFileAppender</font> is two XML properties: `<rollingPolicy>` and `<triggeringPolicy>`: <font color="purple">RollingPolicy</font> is responsible for the rollover procedure (i.e. file moving and renaming) and <font color="purple">TriggerPolicy</font> is what _activates_ <font color="purple">RollingPolicy</font>. Each can be defined separately, but if the <font color="purple">RollingPolicy</font> covers _both_ the <font color="purple">RollingPolicy</font> _and_ the <font color="purple">TriggerPolicy</font> (as `ch.qos.logback.core.rolling.TimeBasedRollingPolicy` does) you only need the <font color="purple">RollingPolicy</font>.  

The `<fileNamePattern>` uses the syntax from [java.text.SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html), optionally allowing you to set a timezone as listed in the ['TZ database name' column on Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) (and if the timezone is not an approved one, UTC is assumed).  In general, the log rolls over once the lowest named time element changes; for example, `waglog.log-%d{yyyy.MM.dd,UTC}` changes the log at midnight (and specifically, midnight UTC).  `<maxHistory>` specifies when the archived files should be deleted - for example, the above will delete archived files after 15 days.  

There are many other options with <font color="purple">RollingPolicy</font> and <font color="purple">TriggerPolicy</font> - feel free to [check them out](http://logback.qos.ch/manual/appenders.html#RollingFileAppender). For example:  
* Its possible to have _multiple_ applications writing to the _same_ log file if you set the `<prudent>` tag to true.  There are some restrictions though:
 * You cannot have a [FixedWindowRollingPolicy](http://logback.qos.ch/manual/appenders.html#FixedWindowRollingPolicy).  
 * File compression is not supported or allowed.  
 * The `<file>` property of `FileAppender` cannot be set and must be blank.  
* Its possible to have a time _and_ size limit if you use `ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy`; for example, this rolls the file over each day _and also_ triggers a rollover if the filesize exceeds 100MB:  
```
    <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
      <fileNamePattern>mylog-%d{yyyy-MM-dd}.%i.txt</fileNamePattern>
       <maxFileSize>100MB</maxFileSize>    
       <maxHistory>60</maxHistory>
    </rollingPolicy>
```  
 * This requires _two_ variables:  
   * `%d` that will handle the date rollover.  
   * `%i` that will handle the fole size rollover.  
 * Each day the log is rolled over.  
 * _If_ the filesize hits 100MB, the file is rolled over and `%i` is incrased by 1.  

## Encoders 

> More on <font color="green">Encoders</font> can be found [here](http://logback.qos.ch/manual/encoders.html), and the full list of options for <font color="green">Encoders</font> can be found [here](http://logback.qos.ch/manual/layouts.html#conversionWord).  

An <font color="green">Encoder</font> is a way for format the log message; its found in an [appender](learn_to_code/java/logback?id=appenders).  Usually, you would simply format the encoder yourself like so:
```
		<encoder>
			<charset>UTF-8</charset>
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS,UTC} %highlight(%level) [%thread] %cyan(%logger{36}) %magenta(%X{akkaSource}) %msg%n</pattern>
		</encoder>
```

> `%X{akkaSource}` is for [Akka](learn_to_code/java/akka/) - you can ignore that one if you are not using Akka.  

The `<pattern>` is the important part; it lays out how the log line should be displayed.  The full list of options is [here](http://logback.qos.ch/manual/layouts.html#conversionWord), but the ones I like most are the following:  
* A datetime (i.e. `%d{yyyy-MM-dd HH:mm:ss.SSS,UTC}`).  The format is the same as [java.text.SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html).  One thing to note is the comma signifies the timezone; I like UTC, but you can pick most things as listed in the ['TZ database name' column on Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).  If the timezone is not an approved one, UTC is assumed.  
* The [level](learn_to_code/java/logback?id=levels-of-severity) (i.e. `%level`).  I like to wrap this in `%highlight()` as that will change `INFO` to blue, `WARN` to red, and `ERROR` to bold red on the screen.  
* The thread (i.e. `%thread`).  The name of the thread.  
* Logger (i.e. `%logger`) - This is the name of the [logger](learn_to_code/java/logback?id=loggers) name _or_ class where the log was generated.  If you take a look at the [example project](learn_to_code/java/logback?id=example-project) you can see that one logger (`logger`) simply uses a name and the second logger (`sec_logger`) actually uses the class identifier. Both of these would show when `%logger` is used (its recommended to use the class identifier).  
* The message (i.e. `%msg`) - The actual message sent to the logger.  
* The newline (i.e. `%n`) - This is critical - without this the log will be one giant line of text.  

Its also possible to use pre-defined encoders; for example, this prints out a JSON-friendly line to the log (pre-formatted):  
```
      <encoder class="net.logstash.logback.encoder.LogstashEncoder"/>
```  

# Loggers 

> More on <font color="green">loggers</font> can be found [here](http://logback.qos.ch/manual/configuration.html#syntax).  

Using <font color="green">loggers</font> can help you refine what is placed in the logs.  

## Naming Convention For Loggers

Loggers must be named in the Java code, these are not named in [logback.xml](learn_to_code/java/logback?id=logbackxml). There are two main ways to name a logger: use a name you make up _or_ use the class name.  

If you wish to use the class name, you create the <font color="green">logger</font> (in the code) like so:  
```
private static final Logger logger = LoggerFactory.getLogger(SomeClassName.class);
```  
* `SomeClassName.class` is the name of the class where the logger is defined (in this case its `SomeClassName`).  

Then, when you write to that logger in the code (i.e. `logger.debug("This is a line printed to the log.");`) the `packagename.class` will identify this origin of message; in the logs, the `logger` tag will show as the package name dot class (i.e. `com.wagenseller.SomeClassName`). The benefit of this is each class has its own identifier, so you know exactly where its coming from.  I prefer this method.  

If you wish to use a name you make up, you can do that like so:
```
private static final Logger logger = LoggerFactory.getLogger("waglog");
```  

Then, when you write to that logger in the code (i.e. `logger.debug("This is a line printed to the log.");`) `waglog` will identify this origin of message. The benefit of this is a grouping (and not just the class) can be identified. 

## Loggers in logback.xml

> Unfortunately, I could not figure out how to get multiple `<logger>` tags to work on an individual [appender](learn_to_code/java/logback?id=appenders) level - I am sure its possible, but I have not figured it out yet.  

We can [name a logger in the log](learn_to_code/java/logback?id=naming-convention-for-loggers), but we can further restrict the log messages in [logback.xml](learn_to_code/java/logback?id=logbackxml) using a `<logger>` tag; in [our example](learn_to_code/java/logback?id=example-logbackxml) we do this with:  
```
	<logger name="waglog" level="${LOGLEVEL_WAG:-INFO}" additivity="false">
        <appender-ref ref="waglogAppender"/>
		<appender-ref ref="STDOUT"/>
    </logger>
	
	<logger name="io.netty.handler.codec.http" level="${LOGLEVEL_NETTY:-WARN}" additivity="false">
        <appender-ref ref="STDOUT"/>
    </logger>	
```

If you do not wish to restrict a specific logger you do not need to take any further action; however, if you wish to further restrict the log file, you must use a `<logger>` tag in [logback.xml](learn_to_code/java/logback?id=logbackxml) and its name _must_ equal the [name of the logger in the log](learn_to_code/java/logback?id=naming-convention-for-loggers).  Using this method will allow you to further restrict based on the [level](learn_to_code/java/logback?id=levels-of-severity) of the message: that level or higher will be printed while lower levels will be ignored (note I used [property variables](learn_to_code/java/logback?id=referencing-variables-in-properties) above). 

If you decide to use the `<logger>` tag, you _must_ list each [appender](learn_to_code/java/logback?id=appenders) with an `<appender-ref>` (see above). If you do not, the named logger will _not_ appear in the appender you left out! 

> I use `additivity="false"` because otherwise the same lines may print to a log twice.  See [here](http://logback.qos.ch/manual/configuration.html#syntax) for more information.  

!> Often you will wish to restrict annoying classes that spam the logs; sometimes you may see package names abbreviated (i.e. instead of `wagenseller.com.SomeClass` you will simply see `w.c.SomeClass`). This is a result of putting length restrictions on the logger name in [the encoder](learn_to_code/java/logback?id=encoders). You will eventually have to write a matching name in [logback.xml](learn_to_code/java/logback?id=logbackxml) to restrict its messages - you _must_ use the **full** classpath here and _not_ the abbreviated path!  

# Root Tag

If you want to print to the log at all, you will need a `<root>` tag in [logback.xml](learn_to_code/java/logback?id=logbackxml); in [our example](learn_to_code/java/logback?id=example-logbackxml) we do this with:  

```
	<root level="INFO">
		<appender-ref ref="waglogAppender" />
		<appender-ref ref="STDOUT" />
	</root>
```  

This is a catch-all for messages; unless otherwise specified (for example in a [logger tag](learn_to_code/java/logback?id=loggers-in-logbackxml)), any message from a log that was not addressed previously will be limited to the given level (or higher). Two things to note:
* You can only have one `<root>` tag.  
* If you do not name the [appender](learn_to_code/java/logback?id=appenders) here that appender will _not_ print at all - so basically you must include all of your appenders here as we did above.  

# Example Project

!> This requires [logback.xml](learn_to_code/java/logback?id=logbackxml) to be accessible to your code (usually it sits at the root level of your jar file).

```
package com.wagenseller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;


public class LogbackExample {

    private static final Logger logger   = LoggerFactory.getLogger("waglog");
	private static final Logger sec_logger = LoggerFactory.getLogger(LogbackExample.class);

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
2019-09-12 01:56:49 INFO [] com.wagenseller.LogbackExample  second stuff is here!
2019-09-12 01:56:49 DEBUG [] waglog  This is a debug message
2019-09-12 01:56:49 WARN [] waglog  This is a WARNING
2019-09-12 01:56:49 ERROR [] com.wagenseller.LogbackExample  CRITICAL ERROR!
```

Note:  
* You can specifically name the logger (as in `waglog` above) _or_ you can use the class name (i.e. `LoggerFactory.getLogger(LogbackExample.class)`). Its recommended to use the class name.  

