# Amazon Speech-to-Text

!> Amazon's speech to text functionality is called <font color="purple">Amazon Transcribe</font>.  

# External Links

## Basic Links

* [What is 'Transcribe'](https://docs.aws.amazon.com/transcribe/latest/dg/what-is-transcribe.html)  
* [here](https://docs.aws.amazon.com/transcribe/latest/dg/API_StartTranscriptionJob.html)  
* [Beginners notes on steaming](https://docs.aws.amazon.com/transcribe/latest/dg/streaming.html)  
* [Java implementation of Streaming Audio](https://docs.aws.amazon.com/transcribe/latest/dg/getting-started-streaming.html)  
* [Creating an Audio Transcript](https://aws.amazon.com/getting-started/tutorials/create-audio-transcript-transcribe/?trk=gs_card)  
* [Working with Amazon Transcribe - bidirectional streaming example](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/examples-transcribe-bidirectional-streaming.html)  
* [How Amazon Transcribe Works](https://docs.aws.amazon.com/transcribe/latest/dg/how-it-works.html)  
* [Java Doc on AmazonTranscribeAsync](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/transcribe/AmazonTranscribe.html)  

## Links Given By Amazon

* [Amazon Transcribe now supports real-time transcriptions](https://aws.amazon.com/blogs/machine-learning/amazon-transcribe-now-supports-real-time-transcriptions/)  
 * There is sample code on GitHub in here
* [Amazon Transcribe Streaming Now Supports WebSockets](https://aws.amazon.com/blogs/aws/amazon-transcribe-streaming-now-supports-websockets/)  

# Amazon Transcribe: Basic Steps

These steps are the very basics of getting <font color="purple">Amazon Transcribe</font> up and running.  

1\. [Sign up](https://docs.aws.amazon.com/transcribe/latest/dg/setting-up-asc.html) for AWS (the actual sign-up page is [here](https://portal.aws.amazon.com/billing/signup#/start)).  
2\. [Set up the AWS Command Line Interface (CLI)](https://docs.aws.amazon.com/transcribe/latest/dg/setup-asc-awscli.html) (see [here](learn_to_code/machine_learning/amazon_speech_and_text?id=setting-up-awscli) for my own personal notes on this).  
3\. [Try out the Console](https://docs.aws.amazon.com/transcribe/latest/dg/getting-started-asc-console.html).  
 * I skipped this option initially, as I didn't quite see the point - this seems to be convoluted when trying this out, I am not sure why you simply cannot provide an mp3 and a web address.  
4\. Getting Started using the API
 * [Using the AWS CLI](https://docs.aws.amazon.com/transcribe/latest/dg/getting-started-cli.html) (this requires that you load an audio file on an s3 bucket).  
 * [Using the SDK in Python](https://docs.aws.amazon.com/transcribe/latest/dg/getting-started-python.html)  
5\. [Getting Started With Streaming Audio](https://docs.aws.amazon.com/transcribe/latest/dg/getting-started-streaming.html)  
 * This utilizes Java (I will expand upon this later).  

# Setting Up awscli	

The program `awscli` can be used to interact with AWS - to do so you will need your <font color="purple">AWS Access Key ID</font>, your <font color="purple">AWS Secret Access Key</font>, your <font color="purple">Default region name</font> and finally your <font color="purple">Default output format</font> (I like <font color="orange">json</font> personally).  

To install on Ubuntu, [switch to root](operating_systems/ubuntu/linux_notes?id=becoming-root), [update all packages](operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages) and then run:
```
apt-get install awscli
```

Once installed, you can set up AWS with the command `aws configure`.  You will need to give:
* AWS Access Key ID  
* AWS Secret Access Key  
* Default region name  
* Default output format  

This creates two files: `~/.aws/credentials` (which is the access key ID / secret access key) and `~/.aws/config` (which saves the default region name and output format).  

# Streaming Example with Java

> The Java AWS SDK 2.0 is located [here](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/welcome.html).  

## Maven Install

[This page](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/setup-project-maven.html) attempts to document the install via Maven (and is where I got some of the info I needed to do this).  The alpha Maven AWS page [is located here](https://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk), but the new mandatory 2.x verion is piecemeal and [is located here](https://mvnrepository.com/artifact/software.amazon.awssdk).    


[The official Amazon Maven page](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/setup-project-maven.html) suggests the use of a <font color="green">bill of materials</font> (<font color="green">BOM</font>), which ensures all other packages use the same version (so if you wish to change the version, just change it once, here). To install, place this in the pom <font color="green">dependencyManagement</font> section (which is _not_ the [dependencies](learn_to_code/java/maven?id=adding-dependencies-to-maven) section):    
```
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>software.amazon.awssdk</groupId>
				<artifactId>bom</artifactId>
				<version>2.X.X</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
```
* I used version `2.10.81`, which came out in March 2020.  

Once this is done, you add dependencies in the [dependencies](learn_to_code/java/maven?id=adding-dependencies-to-maven) section as normal, with the caveat you should _not_ use the version numbers.  You can browse all of the AWS 2.x SDK version [here](https://mvnrepository.com/artifact/software.amazon.awssdk) (the 1.x is old and will soon not be supported).  In order to get the demo up and running, place these in your Maven [dependencies](learn_to_code/java/maven?id=adding-dependencies-to-maven) section:
```
...
		<dependency>
			<groupId>software.amazon.awssdk</groupId>
			<artifactId>protocol-core</artifactId>
		</dependency>
		<dependency>
			<groupId>software.amazon.awssdk</groupId>
			<artifactId>regions</artifactId>
		</dependency>
		<dependency>
			<groupId>software.amazon.awssdk</groupId>
			<artifactId>auth</artifactId>
		</dependency>
		<dependency>
			<groupId>software.amazon.awssdk</groupId>
			<artifactId>transcribestreaming</artifactId>
		</dependency>
...
```

## Code

> This code comes from 

# Bidirectional Streaming Example

The example code can be found [here](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/examples-transcribe-bidirectional-streaming.html).  