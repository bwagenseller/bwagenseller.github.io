# Ant

# Installing Ant

If you wish to know how to install ant on your linux system, [the instructions are here](ubuntu/server_build?id=installing-apache-ant).

The fundamental idea behind Ant is compiling large Java projects with ease; its easy to just use [basic compiling](learn_to_code/java/java_basics?id=basic-compiling) for one .java file, but if you have, say, 50, that can be a headache. In addition, Ant will also allow you to make a .jar file, which you can then use as a reference in another Java project. These two points are the reasons that Ant (or [Maven](learn_to_code/java/java_basics?id=compiling-with-maven)) are used.

# Basics of Ant

Simply put, Ant is a program that takes a specifically formatted XML file and builds a java .jar file to the declared specifications; this .jar file can then be used in other Java projects / can be used as a stand-alone program. Here is a basic file that ant would accept (note I use the [basic java example](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program) from above as the 'project' we are compiling). Note that, by default, we would paste this into a file called `build.xml`:

```
<project name="HelloWorld" default="jar" basedir=".">
	<description>
		This is a sample HelloWorld project build file.
	</description>
	<property name="buildDir" value="build"/>
	<property name="srcDir" value="src"/>
	<property name="classesDir" value="${buildDir}/classes"/>
	<property name="jarDir" value="${buildDir}/jar"/>
	<target name="dirs">
		<mkdir dir="${classesDir}"/>
		<mkdir dir="${jarDir}"/>
	</target>
	<target name="compile" depends="dirs">
		<javac srcdir="${srcDir}" destdir="${classesDir}"/>
	</target>
		<target name="jar" depends="dirs,compile">
		<jar destfile="${jarDir}/HelloWorld.jar" basedir="${classesDir}"/>
	</target>
</project>
```
* Assumptions
 * You can run `ant` from any directory, but there *must* be a file named `build.xml` in that directory (filled out with the above info), and the file 'DragonBorn.java' must be stored in a subdirectory named 'src'.
   * Alternatively, you can name the XML file something else, but you must specify the filename when you run ant like so: `ant -f SomeOtherFileName.xml`.
* The project name set here is 'HelloWorld'.
* The descrtiption is 'This is a sample HelloWorld project build file.' The description can be helpful, as this is what is displayed when a user uses the option 'projecthelp' at the command line.
* The `property` tags are actually variables that will be used in the xml file later on; they are referenced with the `${VARIABLE_NAME_HERE}` format.
 * For example, `<property name="srcDir" value="src"/>` declares the variable `srcDir` and is set to 'src'; later on, we will reference the variable using the format `${srcDir}` and the word 'src' will be used.
* The XML tag `target` specifies an action.
 * The 'name' property can be anything you want it to be, and will be how you reference the target action later.
   * For example, the arbitrary name 'dirs' for the first target means that if the 'dirs' action is called, two directories will be made (in the case above, the directories 'build/classes/' and 'build/jar/' will be made).
 * The 'depends' property means 'these targets must be called before I can be run'.
   * For example, if the 'compile' target is selected, the 'dirs' target would run _first_ (which would create the two directories), and _then_ the 'compile' target would run.
 * The actual actions are laid out in tags within the `<target>` tag.
   * The `<mkdir>` tag makes directories and, in this example, takes one parameter: the name of the directory to be made.
   * The `<javac>` tag actually compiles the code and, in this example, takes two parameters: the source directory (of the .java files) and the destination directory (where the .class files will be stored).
   * The `<jar>` tag actually creates the .jar file for our project and, in this example, takes two parameters: the destination of the completed file and the base directory where the .class files are stored.
* The default target action is 'jar', which is specified in the `<project>` tag.


If we run ant by typing either `ant` (and naming our XML file build.xml) or `ant -f SomeOtherFileName.xml`, we will get the following output:


```
dirs:
    [mkdir] Created dir: /home/brent/temp/build/classes
    [mkdir] Created dir: /home/brent/temp/build/jar

compile:
    [javac] /home/brent/temp/SomeFile.xml:14: warning: 'includeantruntime' was not set, defaulting to build.sysclasspath=last; set to false for repeatable builds
    [javac] Compiling 1 source file to /home/brent/temp/build/classes

jar:
      [jar] Building jar: /home/brent/temp/build/jar/HelloWorld.jar

BUILD SUCCESSFUL
Total time: 3 seconds
```
* The 'dirs' action was run first and two directories were created.
* The 'compile' action was then run, with no errors.
* The 'jar' action was then run and the .jar file was created.

If we run it again we will get this:

```
Buildfile: /home/brent/temp/SomeFile.xml

dirs:

compile:
    [javac] /home/brent/temp/SomeFile.xml:14: warning: 'includeantruntime' was not set, defaulting to build.sysclasspath=last; set to false for repeatable builds
    [javac] Compiling 1 source file to /home/brent/temp/build/classes

jar:
      [jar] Building jar: /home/brent/temp/build/jar/HelloWorld.jar
```
* Notice how the directories were not created this time.


