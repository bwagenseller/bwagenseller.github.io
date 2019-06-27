# Maven

Maven is a (newer, and now more widely-used) tool that can compile Java projects. Many entities wanted to move away from Ant because it was _too_ general. <br>

Note that you [must install Maven](ubuntu/server_build?id=installing-apache-maven) before you use it.

!> It appears that Maven is _very_ specific: so much so that it may be advisable to create the project with Maven _first_, _then_ import to Eclipse (or whatever IDE you are using).

# Locations of Files in Maven

Its possible to change the file structure in Maven, but most do not (and most do not recommend it). The basic file structure is:
* Source (code) files: Directory `src/`
 * Test code: `src/test/`
   * Test Java Code: `src/test/Java/`
   * Resources: `src/test/resources/`
 * Production Code: `src/main/`
   * Production Java Code: `src/main/Java/`
   * Resources: `src/main/resources/`
* Files created by Compiling: Directory `target/`
 * JAR file
 * .class files (in a similar directory to what it is in in the source directory)

# Maven XML File

The XML file that Maven needs to compile the code is pom.xml (Project Object Model). This pom.xml file is _required_ to be in the root directory of your Java project (and your Java project _must_ use the directory structure [as noted above](learn_to_code/java/java_basics?id=locations-of-files-in-maven)).

# Creating a Project with Maven

!> Make SURE that when you create your project that you want your current working directory to be the root directory for your project!

Maven helps you build a skeleton for your project. You can create a Maven project with generating an 'archetype' for a project with the command `mvn archetype:generate` (an archetype is basically an initial code layout). You will now be asked several questions:
* <font color="green">Project Type</font>: Maven will ask 'Choose a number' and will be referencing a project type. It will then list *thousands* of project types; just choose the default (which the ID can change)
* <font color="green">Project Version</font>: Pick what you like, but the best is choice 5 (Version 1.0).
* <font color="green">groupID</font>: A 'group' that this project will belong to; its [usually a domain name reversed](http://maven.apache.org/guides/mini/guide-naming-conventions.html) (so microsoft.com becomes com.microsoft) followed by you company's policy on naming groups. Usually any 'dot' here will represent a folder (so com.sun will become com/sun/ on the server). For our purposes we will choose 'com.vandelay.industries'
* <font color="green">artifactID</font>: This is a description of what the code will be doing; hyphens (-) are usually used instead of underscores. Do NOT include any versions for this. I just called it 'SortTutorial'.
* <font color="green">Version</font>: I am not sure why it asks for another version, but most use [semantic versioning](http://semver.org/), which means 'MajorRelase.MinorRelease.PatchRelease'. Also the term '-SNAPSHOT' is sometimes used at the end which means 'nightly build'; that said, Maven interprets '-SNAPSHOT' as a non-release version, which means it allows multiple versions of this to be released (versions without '-SNAPSHOT' at the end can only be used once, so be careful). Lets name ours '1.0.0-SNAPSHOT'
* <font color="green">Package</font>: Almost exclusively the same as the 'groupID', so use that unless there is some odd reason to use something else.

Maven has now created a 'SortTutorial' folder in your current directory; in SortTutorial/ Maven created the [pom.xml](learn_to_code/java/java_basics?id=maven-xml-file) file. It also created two files: a test filecalled 'AppTest.java' in `SortTutorial/src/test/java/com/vandelay/industries` and a 'main' file called 'App.java' in `SortTutorial/src/main/java/com/vandelay/industries`; these use the groupIDs (and make directories separated by the dots present) combined with the [main and test files that are standard for Maven](learn_to_code/java/java_basics?id=locations-of-files-in-maven).

The file is a skeleton built for testing your code; in fact, Maven has included the junit library 'junit.framework.\*' (pronouncted j-unit), which is meant for unit testing your code.

# Compiling Your Maven Project

To compile the Maven project, go into its root directory (for us thats SortTutorial/) and type:
```
mvn package
```

!> The first time you run this it will download a bunch of pom files - this is fine.

You should see something like this:
```
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by com.google.inject.internal.cglib.core.$ReflectUtils$1 (file:/usr/share/maven/lib/guice.jar) to method java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain)
WARNING: Please consider reporting this to the maintainers of com.google.inject.internal.cglib.core.$ReflectUtils$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building SortTutorial 1.0.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ SortTutorial ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /home/manifest/TEMP_Eclipse/SortTutorial/src/main/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.0:compile (default-compile) @ SortTutorial ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ SortTutorial ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /home/manifest/TEMP_Eclipse/SortTutorial/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.0:testCompile (default-testCompile) @ SortTutorial ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ SortTutorial ---
[INFO] Surefire report directory: /home/manifest/TEMP_Eclipse/SortTutorial/target/surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running com.vandelay.industries.AppTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.007 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] 
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ SortTutorial ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.106 s
[INFO] Finished at: 2019-01-14T21:36:19-05:00
[INFO] Final Memory: 11M/44M
[INFO] ------------------------------------------------------------------------
```

It did multiple things here, but most importantly: it compiled the code, created a [JAR file](learn_to_code/java/java_basics?id=jar-files), and ran unit testing on the code.

This created the `SortTutorial/target/` folder; in that folder is compile-realted files, including a [JAR file](learn_to_code/java/java_basics?id=jar-files) of your project.
<br>
**<font size="4">If Compiling Led to Errors</font>** 

Its possible to fail with errors looking something like this:
```
...
[ERROR] COMPILATION ERROR : 
[INFO] -------------------------------------------------------------
[ERROR] Source option 5 is no longer supported. Use 6 or later.
[ERROR] Target option 1.5 is no longer supported. Use 1.6 or later.
[INFO] 2 errors 
[INFO] -------------------------------------------------------------
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
...
```

This means that your version of Maven is expecting Java 5; you need to tell it that the source code will be in Java 11 and the target (read: compiled) code should be done in Java 11.  To do so, open pom.xml and paste this in between the `<project>...</project>` tags:
```
  <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
          <configuration>
              <source>1.11</source>
              <target>1.11</target>
              <release>11</release>
          </configuration>
        </plugin>
      </plugins>
  </build>
```

Re-run with `mvn package` and it should compile.

# Running The Compiled JAR File

Maven makes a toy 'Hello World' program in App.java; we can run this by calling the project name and then the App class by putting the [JAR file](learn_to_code/java/java_basics?id=jar-files) on the [classpath](learn_to_code/java/java_basics?id=the-classpath) (run this from the `SortTutorial/` folder):
```
java -cp target/SortTutorial-1.0.0-SNAPSHOT.jar com.vandelay.industries.App
```
* The class is 'App'
* The package for the 'App' class is 'com.vandelay.industries'

# Adding JAR files to Maven

Often, you will have to add specific JAR files to your projects; sometimes they are homemade, and sometimes they are officially available to the public via a free library. Whichever the case, you can add either JAR file to your maven project.

## Adding a JAR From a Public Library

Its possible to get publicly available JAR files into maven - they will be downloaded automatically. For the purpose of example, we will use the [Apache Commons CLI](http://commons.apache.org/proper/commons-cli/), which parses arguments on the command line.

Steps:

1\. Search for your JAR file plus the word `maven` in a web browser. Look for the results from the page https://mvnrepository.com - it is from this page that we can get the XML information for our pom.xml file. In our case, I searched for 'Apache Commons CLI maven' and clicked on the https://mvnrepository.com link and got this:

![maven_repository.jpg](images/maven_repository.jpg)

2\. Copy the XML from step 1; in our case, that is:
```
<!-- https://mvnrepository.com/artifact/commons-cli/commons-cli -->
<dependency>
    <groupId>commons-cli</groupId>
    <artifactId>commons-cli</artifactId>
    <version>1.4</version>
</dependency>
```

3\. Open the associated Maven `pom.xml` file and look for the `<dependencies>...</dependencies>` tags. Paste the contents from step #2 just before the last dependency. Save the file.

Now, if `mvn package` is ran, the JAR file will be automatically downloaded!


---

# Maven In Eclipse IDE

Its possible to use Maven in the Eclipse IDE. Here is a short demo on using it.

## Installation of Maven in Eclipse

Maven usually comes installed in Eclipse, but if its not there for some reason, [here is how you install it](ubuntu/server_build?id=installing-the-eclipse-maven-add-on) in Eclipse.

## Building a New Maven Project in Eclipse

There are two ways you can initiate a Maven Project: By Clicking 'File->New->Project...' OR by right-clicking on the 'Packagte Explorer' area and then clicking 'New->Project...'. Both are below:<br>
<span style='width: 350px; display:inline-block'>![New Project - First Method](images/MavenInEclipse1_NewProject.jpg)</span> <span style='width: 350px; display:inline-block'>![New Project - Second Method](images/MavenInEclipse2_NewProject.jpg)</span> <br>

Now pick 'Maven'/'Maven Project':
![New Project - Maven](images/MavenInEclipse_PickMaven.jpg)

There are some options on the next screen, but I just click 'Next': <br>
![New Project - One](images/MavenInEclipse_NewMavenProject1.jpg)

Now you will have to pick an archetype (an archetype is basically an initial code layout); if you don't know what this is, you should simply pick the default archetype (groupID 'org.apache.maven.archetype', artifact ID 'maven-archetype-quickstart', Version '1.1'). If you do not have problems, you will see the selection on the left; however [if you have problems initializing Maven](learn_to_code/java/java_basics?id=problems-initializing-eclipse-maven) you will see the screen on the right, and you will have to manually pick your archetype. Do so and press 'Next': <br>
<span style='width: 350px; display:inline-block'>![New Project - First Method](images/MavenInEclipse_NewMavenProject2.jpg)</span> <span style='width: 350px; display:inline-block'>![New Project - Second Method](images/MavenInEclipse_NewMavenProject2a.jpg)</span> <br>

Finally you can fill out the unique information about your Maven project; use the information we filled in for [our project above](learn_to_code/java/java_basics?id=creating-a-project-with-maven) and click 'Finish': <br>
![New Project - Finish Maven Creation](images/MavenInEclipse_NewMavenProject3.jpg)

If it works, you will see the setup is the same as it was [when the command version created the project](learn_to_code/java/java_basics?id=creating-a-project-with-maven); even the [directories], the location of the pom.xml and generated JAR file, etc should be the same: <br>
![Maven Final Setup](images/MavenInEclipse_ComparisonToMaven.jpg)

!> You may have to configure the file pom.xml for Java 11 [as you did when you compiled manually](learn_to_code/java/java_basics?id=compiling-your-maven-project).

### Problems Initializing Eclipse-Maven

> This section was influenced by the discission on [stackoverflow](https://stackoverflow.com/questions/15506043/create-a-maven-project-in-eclipse-complains-could-not-resolve-archetype).

Its possible that there are problems when trying to start a Maven project in Eclipse; the biggest issues seem to stem from firewall / proxy issues, so if your firewall or proxy does not allow for the connection to common Maven sites you may have to find an alternative.

When starting a Maven Project in Eclipse you may see something like this:
```
...
Could not resolve archetype org.apache.maven.archetypes:maven-archetype-webapp:RELEASE from any of the configured repositories.
Could not resolve artifact org.apache.maven.archetypes:maven-archetype-webapp:pom:RELEASE
Failed to resolve version for org.apache.maven.archetypes:maven-archetype-webapp:pom:RELEASE: Could not find metadata org.apache.maven.archetypes:maven-archetype-webapp/maven-metadata.xml in local ....
...
```

If you see errors like this, the first thing you should do is add the remote catalog http://repo1.maven.org/maven2/archetype-catalog.xml to the Maven Archetypes. To do this, Click 'Window->Preferances', then click 'Maven->Archetypes':
![Maven Archetypes](images/MavenInEclipse_AddArchetypes.jpg)

Click the 'Add Remote Catalog' button, then add  http://repo1.maven.org/maven2/archetype-catalog.xml to the 'Catalog File' and 'maven catalog' to the 'Description. Click 'Apply and Close'. <br><br>

You may also have to alter the file 'settings.xml', which is in your home directory in the folder `.m2` (so `~/.m2/settings.xml`). Add the following to that file:
```
<mirror>
  <id>ibiblio.org</id>
  <url>http://mirrors.ibiblio.org/maven2</url>
  <mirrorOf>central</mirrorOf>
</mirror>
```

You will then have to tell Eclipse to re-load this file. To do this, Click 'Window->Preferances', then click 'Maven->User Settings': <br>
![Maven XML Settings](images/MavenInEclipse_MavenXMLSettings.jpg)

Simply click the 'Update Settings' button, then 'Apply and Close'.

If you still cannot create a Maven project, [read more about it here](https://stackoverflow.com/questions/15506043/create-a-maven-project-in-eclipse-complains-could-not-resolve-archetype).

## Running Maven In Eclipse

> This info was found [here](http://www.vogella.com/tutorials/EclipseMaven/article.html#download-the-maven-index).

To run the code as a Maven project, right-click the pom.xml file and select 'Run as...'

![Run As Maven](images/MavenInEclipse_RunMaven.jpg)


There are many options; [this article on stackoverflow](https://stackoverflow.com/questions/37999457/what-is-the-difference-between-maven-install-and-maven-build-with-m2eclipse) talks about most of them.  For our purposes, stick to 'Maven Clean' for now.

