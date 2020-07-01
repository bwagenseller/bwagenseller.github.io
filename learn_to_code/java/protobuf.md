# Protobuf (Google)

> A good introduction can be found [here](https://developers.google.com/protocol-buffers/docs/javatutorial).

# What is protobuf?

<font color="green">Protobuf</font> (more formally Google's <font color="green">Protocol Buffer</font> and sometimes called '<font color="green">protoc</font>' (the name for the standalone binary for <font color="green">Protobuf</font>)) are meant for serializing structured data; it is like XML, although more compact (but not as easily written as XML). In other words, if you have some data that needs to be sent to a foreign machine, <font color="green">Protobuf</font> may be helpful to you. Protobuf creates structured data, and this data can be shared by multiple languages (Java, Python, Go, C#, C++, and more). The power of <font color="green">Protobuf</font> is you can create an object in, say, Java, and then send that object to C++ and as long as <font color="green">Protobuf</font> was used to create the data structure in both the Java implementation and C++, the data can be shared easy between the two.

While sharing data structures across the wire is the main point of Protobuf, you can also use it to quickly make classes in various languages so long as there is no unique code needed; Protobuf will take care of storing the various data types, getters, setters, comparisons, hashes, etc.

> I will focus on Protobuf in Java, although the `.proto` files are built exactly the same for any language.

# Protobuf Links

* [Protobuf Main Page](https://developers.google.com/protocol-buffers/docs/proto3)  
* [Scalar Value Types](https://developers.google.com/protocol-buffers/docs/proto3#scalar)  
* [Using other message types (also defined in the .proto file)](https://developers.google.com/protocol-buffers/docs/proto3#other)  


# Installing Protobuf

!> You MUST install Protobuf on the machine where you are compiling the code (even Maven _will_ need the `protoc` binary in order to create the `.java` files from the `.proto` files). You will probably not have to install it on the machine where the code runs, as it should only be needed to convert the `.proto` files.

## Installation on Ubuntu

[Follow these instructions](operating_systems/ubuntu/server_build?id=installing-protobuf) to install Protobuf on Ubuntu.

# Protobuf In Maven

## Maven Plugin

To install Protobuf in Maven, you must include the Maven plugin for Protobuf in your pom.xml file (in the plugins section):
```
			<plugin>
				<groupId>org.xolstice.maven.plugins</groupId>
				<artifactId>protobuf-maven-plugin</artifactId>
				<version>0.5.1</version>
				<configuration>
					<clearOutputDirectory>false</clearOutputDirectory>
					<outputDirectory>src/main/java</outputDirectory>
					<protocExecutable>/usr/local/bin/protoc</protocExecutable>
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
```
* You can change the version if you wish.


## Maven Dependencies

You will _also_ need to include dependencies. One option is to include Protobuf itself and the `gson` library:
```
		<dependency>
			<groupId>com.google.protobuf</groupId>
			<artifactId>protobuf-java</artifactId>
			<version>3.5.0</version>
		</dependency>			
		<dependency>
			<groupId>com.google.code.gson</groupId>
			<artifactId>gson</artifactId>
			<version>2.7</version>
		</dependency>		
```
* You can change the versions if you wish.
* `gson` is probably not required, but it is recommended.

Instead, I like to simply install `protobuf-java-util`, which installs `protobuf-java`, `protobuf-java-util`, `gson`, and a few other packages by default:
```
		<dependency>
			<groupId>com.google.protobuf</groupId>
			<artifactId>protobuf-java-util</artifactId>
			<version>3.5.0</version>
		</dependency>	
```
* You can change the version if you wish.


## Protobuf File Location (Maven)

If using Maven, place your `.proto` files in `src/main/proto`; if you have correctly configured the Maven plugin, when you [compile your Maven project](learn_to_code/java/maven?id=compiling-your-maven-project) Maven will automatically convert the `.proto` files to `.java` files, place these files in `src/main/java` (at least it will use this directory as a base), and then run its checks on the code. 

When compiling the jar file, Maven will place all `.proto` files at the root of the compiled jar file.



