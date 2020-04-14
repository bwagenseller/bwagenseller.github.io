# Learning To Code: Java

# Before We Begin

This documentation is meant for those that have at least a basic grasp of how to write general code (so you understand what 'compile' and 'run' means, you understand 'if' conditionals, 'while' and 'for' loops, etc). This documentation is to teach people in this category the general concepts of Java first, and then we will start writing some code. <br> <br>

Java has a basic formula for writing an application: you will create a [basic Java file](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program) that has [a class](learn_to_code/java/java_basics?id=java-classes); you will [compile](learn_to_code/java/java_basics?id=basic-compiling) your code, and then [run](learn_to_code/java/java_basics?id=basic-script-running) it. You can re-use code with [JAR Files](learn_to_code/java/java_basics?id=jar-files) if you wish.

## Useful Links

* [Here](https://javarevisited.blogspot.com/2011/04/top-20-core-java-interview-questions.html) is a helpful link on frequently asked Java questions in a coding interview.  

## Why I Do This

I find that I have to write things down, in a format I understand, in order for me to fully understand a topic; therefore I take notes on subjects I try to learn. These notes are an artifact of that line of thinking.

## My Note Sources

My main note sources are various code examples I have seen on the web, [stackoverflow](https://stackoverflow.com/), and the Packt book [Java Projects (Second Edition) by Peter Verhas](https://www.packtpub.com/application-development/java-projects-second-edition).

## Java Version

This document was made for Java version 11.

## Installing Java

I wrote how to install java either [with the default jre](operating_systems/ubuntu/server_build?id=install-java-jre) or [from Oracle](operating_systems/ubuntu/server_build?id=installing-java-from-oracle).

## Basic File Setup / First Java Program

The basic file setup for a basic java program is:

```
package dragons;

public class DragonBorn {
	private static AtomicInteger dragonSoulCount =  new AtomicInteger();
	private int level;
	
	//This is a constructor - put things in here that must be initialized
	DragonBorn() {
		dragonSoulCount.set(0);
		level = 1;
	}

	public static void witnessDragonBorn() {
		System.out.println("You.....you took it's soul.");
	}

	public void increaseSoulsTaken() {
		//increase soul count by 1
		dragonSoulCount.incrementAndGet();
	}
	
	public int showSoulCountTaken() {
		return dragonSoulCount.get();
	}
	
	public void increaseLevel() {
		level++;
	}	
	
	public int showLevel() {
		return level;
	}		
	
	public static void main (String[] args) {
		DragonBorn.witnessDragonBorn();
	}

}
```
* The file extension for this file must be .java.
* Classes are a big thing in java; [we will discuss more later](learn_to_code/java/java_basics?id=java-classes), but for now just know a class is a collection of functions and variables that are centered around a similar idea. I have named the class here 'DragonBorn', but feel free to name it whatever you want.
 * Know that whatever you name the class you must ALSO name the file that same thing; so for example, the above MUST be in a cile named 'DragonBorn.java'.
* If we want the .java file to be run-able (sometimes you do not want to directly run it, but other times you do), you MUST include a function called `public static void main (String[] args) { }` as we did above. Whatever is in this will run when [we run it](learn_to_code/java/java_basics?id=basic-script-running).
* The `main` function (`public static void main (String[] args)`) is the code that is launched when called from the command line.
 * `main()` must be [static](learn_to_code/java/java_classes?id=defining-static-methods-in-classes); unfortunately, if a method is `static` it cannot call any non-static methods, so the initial class that holds the `main()` must be pretty much bare-bones, only call other static methods in itself, _or_ create a copy of itself, sending a new instantiated object of itself to its own starting method.
   * For example, if you had a `start()` method that handled the contents of `main()` above you could simply write `new DragonBorn().start(args);` in your `main()` method, which creates an instance of itself that _can_ call non-static methods.
* The method `DragonBorn()` is a [constructor](learn_to_code/java/java_classes?id=class-constructors).
* The variable `dragonSoulCount` - and its associated methods `increaseSoulsTaken()` and `showSoulCountTaken()` - are a bit advanced, but you can read a bit more about them [here](learn_to_code/java/java_classes?id=defining-static-variables-in-classes).

> If you want to use the `module-info.java` file (see below), you *must* include a 'package' as well (in the above code this is defined as 'package dragons;'). The package name *must* be a module in the file `module-info.java`. However if you do not use `module-info.java` and are just writing a quick program, you do not have to declare a 'package' in your .java file (and for small things I would not recommend it, as [there are some consequences / things you have to do differently when using a package](learn_to_code/java/java_basics?id=notes-on-using-module-infojava)).


Another file named `module-info.java` is also usually required (for more complex Java projects):

```
module dragons {
	requires someOtherPackage;
}
```
* The module MUST be named (here we name it 'dragons'; also note you are pretty much forced to use a lowercase letter for the first letter.
* You can require other packages here. This is important when you want to reference other packages.

## Notes On Using module-info.java

Using `module-info.java` will have some consequences:
* You MUST have a 'package' name for every .java file you write (i.e. you must declare a package like 'package dragons;' does above) AND the package name in the .java file MUST be present as a 'module' name in module_info.java.
* You will still have to use the 'include' statement in your .java file if you are referencing other classes on the classpath (it seems that if you do not declare a package and do not use a `module-info.java` file, you may not have to 'include' it).
* If you are using a [JAR file](learn_to_code/java/java_basics?id=jar-files):
 * If that JAR file was built prior to Java 9 - or if it does not have a package name - _in addition to including it on the classpath_, you MUST require it in the `module-info.java` file as well;  for example, if your current Java project package name is 'somePackageName' and you included the file `SomeJAR.jar` in the classpath, you MUST require SomeJAR in the module somePackageName in the file `module-info.java` like so:
```
module somePackageName {
		requires SomeJAR;
}
```
 * Note you do NOT include the .jar file type - just the first part of the name.
   * Java will give you a warning that this may be ambiguous, but you may not have any other option for JAR files pre-Java 9 (and it will still work). 
	   
# Java Classes

Java classes are a bit overwhelming but the truth is, they are fairly straightforward. A class can be considered a grouping of things; so for example, there could be a 'building' class/group, and members of this group are things like house, skyscraper, shed, outhouse, etc. The idea of a 'Building' is a bit vague - and at first, classes are a bit vague - but things that are _defined_ as a building - a house, for example - is a bit more granular.

Early in this document I defined a [DragonBorn class](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program); this was named from a concept in one of my favorite video games, [The Elder Scrolls V: Skyrim](https://en.wikipedia.org/wiki/The_Elder_Scrolls_V:_Skyrim). The [Dragonborn](http://elderscrolls.wikia.com/wiki/Dragonborn_%28Lore%29) refers to someone born with the blood and soul of a dragon, but the body of a mortal human; there are multiple people who were 'Dragonborn' throughout the history of Skyrim.

So, 'Dragonborn' is a class, but any _individual_ instance of a 'Dragonborn' is not a class, but a specific person (such as the main character in Skyrim). 

I will not get much into classes here, but my section on [Java classes](/learn_to_code/java/java_classes) has much more info on them.  

> For more on Java classes [see here](/learn_to_code/java/java_classes).  

---

# Basic Compiling / Running Code

In order to actually run the code, you must compile it (with the `javac` command) and then run it (with the `java` command). We will briefly discuss this here.

## Basic Compiling

The basic way to compile is `javac NAME_OF_JAVA_FILE_GOES_HERE.java`. 

That said, this can _quickly_ become overwhelming for larger projects; its best to compile using [Ant](learn_to_code/java/ant), [Maven](learn_to_code/java/maven), or Gradle.

## Advanced Compiling

More often than not, your projects will be so massive that you will not want to compile dozens - or even hundreds - of .java files with `javac`. The oldest way to do this is 'make', but this is not often used in Java anymore. The two largest tools that are used to compile Java code are [Ant](learn_to_code/java/ant) and [Maven](learn_to_code/java/maven) (my personal favorite).

## The Class File

Compiling the Java file will create a .class file in the same directory (with the same name, sans extension), which will be a binary file. Its this .class file that actually runs when we run the .java file.

> It is easy to ge confused by a class and the class file; the class file is a compiled file that contains the 'machine code' of the class. If I ever reference the .class (with the dot), I am referencing the .class file.

## Basic Script Running

There seems to be two different methods of running Java code: when a 'package' is declared in the .java file and when there is no 'package' declared.

**<font size="4">When a Package Name is Declared</font>**

When a package name is declared in the .java file, after you have [compiled the single file](learn_to_code/java/java_basics?id=basic-compiling), you can run it simply by running `java NAME_OF_JAVA_FILE_GOES_HERE.java`. Note that, even though the [class file is actually what runs](learn_to_code/java/java_basics?id=the-class-file), we still reference the .java file here anyway.

**<font size="4">When a Package Name is Not Declared</font>**

If you try to run the file name when you declared a 'package SOMENAME;' in the .java file (that is to say, if you try to run the file with `java NAME_OF_JAVA_FILE_GOES_HERE.java`), you will see this error:
```
error: class found on application class path: DragonBorn
```

Instead, when you do not declare a package name, simply lop off the .java from the file name:
```
java NAME_OF_JAVA_FILE_GOES_HERE
```


## The Classpath

The *classpath* is simply a listing of locations to look for .class files; we can put this listing together and then pass it to Java. This will enable us to reference multiple, distributed .class files anywhere on the hard drive (or server). For example, lets say we wrote an awesome class called 'AwesomeDateTime' last week and stored it to the Unix directory '/home/brent/awesomefunctions'. If we put '/home/brent/awesomefunctions' in the classpath, we could reference the class 'AwesomeDateTime' in any other Java program!

You list the class path as an option after the `java` command; so for example, if we wanted to include our new class into our DragonBorn class (for the sake of the argument, say we used 'AwesomeDateTime' in the DragonBorn class), we would compile our DragonBorn class like so:
```
javac -cp '/home/brent/awesomefunctions' DragonBorn.java
```

And then to run it we would use:
```
java -cp '/home/brent/awesomefunctions' DragonBorn.java
```

If you wanted to add multiple paths, just list them and separate them with a ':' in Unix (or a semicolin ';' in Windows).

!> When I tested this, I did *not* have to do any sort of 'import' statement in DragonBorn.java to use AwesomeDateTime, it just 'worked'. Note that I did not declare a package name [like I did in the example 'java file](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program).

# JAR Files

A Java JAR file is a collection of compiled Java classes that act as a Java application (a file with  a.jar extension); it houses Java classes that can actively be imported (and then used) in other Java projects, and (in many cases) it can directly run code. JAR files can also be thought of as a pre-compiled Java 'zipped' file: it and can be opened as a compressed file, and if you do you will likely see a directory structure (like a zipped file) with a collection of various [compiled class files](learn_to_code/java/java_basics?id=the-class-file).

## MANIFEST.MF

All JAR files must have a `MANIFEST.MF` file; this file is usually in the folder `META-INF/` in the JAR file. At the least, this file contains meta information about the JAR file.

## Creating a JAR File

To create a JAR file, you must first [compile the .java file](learn_to_code/java/java_basics?id=basic-compiling) and then use the command `jar` like so (we will use the [basic Java file from above](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program), but take care to NOT include the 'package' (so delete 'package dragons;' from that file)):
```
jar -cf dragon.jar DragonBorn.class
```
* The 'c' tells java to create a new JAR file.
* The 'f' names the new JAR file.
* All classes listed will be included in the JAR file.

## Running Java from a JAR File

To run a Java appication from a JAR file, you must specify the class in the file (and that class *must* have a main() function):
```
java -cp dragon.jar DragonBorn
```
* Note we are technically putting our JAR file on [the classpath](learn_to_code/java/java_basics?id=the-classpath) and then running it.


## Using JAR files in the Classpath

You can also use JAR files (will be explained shortly) in the classpath, which means you will *also* have access to it's classes in your own Java code; the major difference is since JAR files are 'archives' (like a ZIP file), a JAR file acts like a directory itself; therefore, when putting a JAR file on the classpath you specify the _name_ if the JAR file as well (if you do not, `javac` will error out).

For example, if we packaged the class 'AwesomeDateTime' in a JAR file called 'AwesomeDateTime.jar' in the directory '/home/brent/awesomefunctions' and wanted to reference it in our DragonBorn.java file, we would have to compile DragonBorn.java like so:
```
javac -cp '/home/brent/awesomefunctions/AwesomeDateTime.jar' DragonBorn.java
```

And then to run it we would use:
```
java -cp '/home/brent/awesomefunctions/AwesomeDateTime.jar' DragonBorn.java
```

!> Be careful when declaring a package in a .java file / using a `module-info.java` file - [you will have to handle JARs differently](learn_to_code/java/java_basics?id=notes-on-using-module-infojava).


# Variables

Basic variables are declared like so:
```
int someOtherVar = 10;
```

General notes:
* Variables are typed - so once they are declared, the type cannot be changed.
* In place of the type on the left, you can simply use `var` and then whatever is assigned on the right the variable will assume that type; _this can be useful_. Example: `var i = 10;` typecasts i to an integer.
 * This may be only available to local variables!
* Variables are only visible in the code block they are assigned (be it function or even things like if/for/while statements).
* Variables are either primitive or reference.
* A local variable can be defined `final` (example: `final String name = "Jack";`) if it will be assigned exactly _once_ in that code block.
 * Each time the code block is executed - say, in a FOR loop - its considered a 'new' block.
 * This is done usually for ease of reading the code and nothing more.
 * Do _NOT_ confuse this with a final class / method OR a final field!

## Primitive Variables

The primitive variables are: <font color="orange">byte</font>, <font color="orange">short</font>, <font color="orange">int</font>, <font color="orange">long</font>, <font color="orange">float</font>, <font color="orange">double</font>, <font color="orange">boolean</font>, and <font color="orange">char</font>.

Sometimes, though, primitive types are required to be a class; therefore a primitive can be converted to  its matching class type using a concept called **auto-boxing**. The corresponding classes are Byte, Short, Integer, Long, Float, Double, Boolean, and Character.

!> Primitive variables can _never_ be `null`.

**<font size="4">Typecasting Primitive Variables</font>**  

Often, you will try to, say, divide one integer by another and find that the result is _always_ an integer; this is because you need to subtly cast the number from an `int` (or `long`, or whatever) to one that has a decimal point.  This is easy to do in Java - any literal involved you can turn into a `double` - which will turn the entire result into a `double` - simply by adding a decimal point.  Here is an example:  
```
long mytime = 18000000;
double myPosition2 = (mytime/1000.0/59.0/5.0);
```  
* With the addition of the `.0` to some of the hardcoded numbers, the result will not be of type `long` but will be a `double`.  

Conversely, you can just typecast _one_ of those numbers as a `(double)` which will have the same effect:  
```
double myPosition3 = (mytime/((double) 1000)/59/5);
```  

Here is an extended example:  
```
        long mytime = 18000000;
        double myPosition = (mytime/1000/59/5);
        double myPosition2 = (mytime/1000.0/59.0/5.0);
        double myPosition3 = (mytime/((double) 1000)/59/5);
        System.out.println(myPosition + "   " + myPosition2 + "     " + myPosition3);
```  

This prints:  
```
61.0   61.016949152542374     61.016949152542374
```

## Atomic Variables

There are variables that cannot act as a direct stand-in for [primitives](learn_to_code/java/java_basics?id=primitive-variables). These are:
* AtomicInteger
* AtomicBoolean
* AtomicLong
* AtomicDouble
 * AtomicDouble is only available in the package `com.google.common.util.concurrent` - its not native to Java.

Atomic variables offer:
* Concurrency protection.
* Incrementing and decrementing in-place.

This second point is important when it comes to moving variables by reference; most variables in Java are passed by reference, with the exception of primitives; that said, some data structures (like [Maps](learn_to_code/java/java_data_structures?id=maps)) bar the usage of primitives and require a class (so people use things like `Integer` instead of `int`); the problem with this is class primitives are immutable - you cannot change it without overwriting it: so a variable `Integer aa` cannot be updated via `aa++;` without destroying the original object and re-creating it (the compiler changes this to something like `aa.intValue()++;`).  The problem is, `aa++` is now not the same object, so its position in the Map is now lost - the position in the map must be over-written with the new value manually.  

Atomic variables get around this, as they can update in place- for example, the following does all calculations in-place, so the reference point never changes for the object:
```
AtomicLong aLong = new AtomicLong(0);
aLong.incrementAndGet();//adds 1
aLong.addAndGet(4);//adds 4
aLong.decrementAndGet();//adds 1
System.out.println("aLong: " + aLong.get());
```
* This prints `4`.  

## nulls

NULL is represented as `null` in code; primitives _cannot_ be null, but Strings - and every other class - can be `null`.

You can check for `null` with a simple `==` operator (as, in the case of classes that are not strings, `==` checks for the location in memory - if its not set, it will be `null`).

## Final Variables

If a variable is marked as <font color="green">final</font>, it can only be initialized _once_ - if you try to initialize it after that you will get an error.

For example, this will work:
```
final int someInt = 5;
```

And this will work:
```
final int someInt;
someInt = 10;
```

...but this will _not_ work:
```
final int someInt;
someInt = 10;
someInt = 5;
```
* This will error out when trying to initialize to 5, as it already initialized to 10.

Note this does _not_ mean whatever is set is _immutable_, it just means it cannot be _initialized_ again. [Primitive variables](learn_to_code/java/java_basics?id=primitive-variables) are _effectively_ immutable, as you can basically only set them or use them; that said, it sometimes makes sense to set [objects built from classes](learn_to_code/java/java_basics?id=java-classes) as final; while it is true they can only be initialized once, any internal variables can be set as many times as you like.

# Primitive Math Tricks

## Floor

Its possible to find the floor of a number simply by casting the result to an (int); for example:  
```
int myNumber = 36;
double myResult = (myNumber/23.0);
double myResult2 = (int) (myNumber/23.0);
int myResult3 = (int) (myNumber/23.0);
System.out.println(myResult + "   " + myResult2 + "    " + myResult3);
```  
This prints:  
```
1.565217391304348   1.0    1
```  

> Realistically, you probably will not have many hard-coded values so you will rely on the `(int)` casting.  

# Strings

Strings are actually classes / objects with functions, so much of how they are treated are just like objects; in most cases they can also be treated like primitives, although they are not primitive. Strings are usually encased in double quotes; for example, `"This sentence is a string in Java"`; more confusingly, numbers wrapped in double quotes (like this: `"139"`) are _also_ considered to be strings. Anything wrapped in double quotes is a string, which can be manipulated as a variable.

> Strings _can_ be `null`.

Since strings are a bit of an odd duck, you can directly call functions from the double quotes. For example, if you wanted for force a string to be upper case, you would normally do something like this:  
```
String myString = "Hello.";
myString = myString.toUpperCase();
System.out.println(myString);
```  
* Prints `HELLO.` 

But you can apply the `toUpperCase()` method to the double quoted section as well:  
```
System.out.println("Hello.".toUpperCase());
```  
* Prints `HELLO.` 

This will work with any method designed for strings.  

> Since strings are objects, you can use methods written for the `String` class, even on 'bare' strings!  

## Combining Strings

```
//Strings can be combined with the + operator
String str1 = "Brent";
String str2 = "Jackson";
System.out.println(str + " and " + str2 + " went to the park.");
```
* Note that it seems you cannot use the single quote to wrap a string (at least in Java 8).

## Finding NULL And Blank Strings 

!> Under most circumstances, if you try any operation - including methods in the `String` class - on a `null` string, Java will throw an exception. To avoid this, its best to check and see if the `String` object is `null` after it is set before anything else is done.


**<font size="4">Check if Empty - isEmpty()</font>**  

In order to check and see if a string is empty - that is to say, is `""` which is defined but _NOT_ `null` - you can use `isEmpty()`. Example:  
```
String myString = "";
if (myString.isEmpty()) System.out.println("The condition was true.");
```   

> If you try to use `isEmpty()` on a string that was never set / is set to `null`, Java will throw an error.  

**<font size="4">Check if Blank - isBlank()</font>**  

In order to check and see if a string is blank - that is to say, is `""` _or_ is comprised of whitespace characters but _NOT_ `null` - you can use `isBlank()`. Example:  
```
String myString = "       ";
if (myString.isBlank()) System.out.println("The condition was true.");
```   

_How is this different from `isEmpty()`_? This is different from `isEmpty()` because `isBlank()` _can_ accept whitespaces (and only whitespaces) while `isEmpty()` cannot accept whitespaces.  

> If you try to use `isBlank()` on a string that was never set / is set to `null`, Java will throw an error.  

**<font size="4">Check if Null Or Empty - !Strings.isNullOrEmpty()</font>**  

> When picking the import, pick `com.google.common.base` which will import `com.google.common.base.Strings`.  

Google's common base Java code has a function that can detect nulls _or_ empty strings (i.e. `null` or "") in one line simultaneously: `!Strings.isNullOrEmpty(someStringHere)`.  For example:  
```
package com.wagenseller;

import com.google.common.base.Strings;

public class Main {

    public static void main(String[] args) {

        String str1 = "something";
        String str2 = "";
        String str3 = " ";
        String str4 = null;

        if(!Strings.isNullOrEmpty(str1)) System.out.println("str1 is NOT empty or null!");
        else System.out.println("str1 IS empty or null!");

        if(!Strings.isNullOrEmpty(str2)) System.out.println("str2 is NOT empty or null!");
        else System.out.println("str2 IS empty or null!");

        if(!Strings.isNullOrEmpty(str3)) System.out.println("str3 is NOT empty or null!");
        else System.out.println("str3 IS empty or null!");

        if(!Strings.isNullOrEmpty(str4)) System.out.println("str4 is NOT empty or null!");
        else System.out.println("str4 IS empty or null!");
    }
}
```  

Prints the following:  
```
str1 is NOT empty or null!
str2 IS empty or null!
str3 is NOT empty or null!
str4 IS empty or null!
```  

For this to work, you will need to include this dependency in your [Maven dependency section](learn_to_code/java/maven?id=adding-dependencies-to-maven):  
```
        <dependency>
            <groupId>com.google.inject</groupId>
            <artifactId>guice</artifactId>
            <version>4.1.0</version>
        </dependency>
```

!> If you are using this Google library only for this function, you may simply want to stick with `((someStringHere != null) && (!someStringHere.isBlank()))`.  Might as well not import that entire library for this one method.  

## Trimming Whitespace

Often, we wish to eliminate leading whitespace, trailing whitespace, or both from a string - there are several methods to do this.  

**<font size="4">Eliminate All Whitespace - strip()</font>**  

> This is currently the accepted method of trimming whitespace in Java for general-purpose use.  

If you wish to eliminate whitespace at both the beginning _and_ end of a string, its best to use `strip()` - here is an example: 
```
String myString = "   hello   programmer    ";
String otherString = myString.strip();
System.out.println("aaa" + otherString + "bbb");
```  

**<font size="4">Eliminate All Whitespace - trim()</font>**  

> In most cases, you will want to use `strip()` aover `trim()` - this is because `trim()` is the older version of the two and only eliminates characers less than `spacebar` on the ASCII chart, whereas `strip()` includes more modern-day characters as well. In short: if you want to use `trim()`, most of the time you will actually want to use `strip()` instead.  

If you wish to eliminate whitespace at both the beginning _and_ end of a string, `trim()` can be used - here is an example: 
```
String myString = "   hello   programmer    ";
String otherString = myString.trim();
System.out.println("aaa" + otherString + "bbb");
```  

**<font size="4">Eliminate Leading Whitespace - stripLeading()</font>**  

If you wish to eliminate whitespace _only at the beginning of a string_, `stripLeading()` can be used - here is an example: 
```
String myString = "   hello   programmer    ";
String otherString = myString.stripLeading();
System.out.println("aaa" + otherString + "bbb");
```  

**<font size="4">Eliminate Trailing Whitespace - stripTrailing()</font>**  

If you wish to eliminate whitespace _only at the end of a string_, `stripTrailing()` can be used - here is an example: 
```
String myString = "   hello   programmer    ";
String otherString = myString.stripTrailing();
System.out.println("aaa" + otherString + "bbb");
```  

## Length

To find the string length, simply use `.length()` as follows:
```
String myString = "hello!";
System.out.println("The number of characters in 'myString' equals " + myString.length() + ".");
```  
* There are 6 characters: `h` `e` `l` `l` `o` `!`  

## Converting to Upper / Lower Case  

It is often convienent to convert a string to either upper or lower case - this can be done with `toUpperCase()` and `toLowerCase()`. Here is an example:  
```
String myString = "Hello, Traveller!";
System.out.println(myString);
System.out.println(myString.toUpperCase());
System.out.println(myString.toLowerCase());
```  

This prints:  
```
Hello, Traveller!
HELLO, TRAVELLER!
hello, traveller!
```  

## String Comparison

Here are a few ways you can compare strings. For the pruposes of this example we will define the following variables (and set them equal to a value):
```
String str1 = "Brent";
String str2 = "Brent";
String str3 = "brent";
String str4 = "Jackson";
```

**<font size="4">compareTo(String anotherString)</font>**  

The `compareTo()` method gives an integer as a return. 
* If its less than 0, the main string object is before / is 'greater than' the string passed to it.  
* If its 0, the strings are equal.  
* If its greater than 0, the main string object is after / is 'less than' the string passed to it.  

Example: 
```
if (str1.compareTo(str4) > 0) {
	System.out.println("'" + str1 + "' comes after '" + str4 + "'");
} else if (str1.compareTo(str4) < 0) {
	System.out.println("'" + str1 + "' comes before '" + str4 + "'");
}
```

**<font size="4">compareToIgnoreCase(String str)</font>**  

`compareToIgnoreCase()` is similar to `compareTo()`, but obviously ignores the case:  
```
if (str3.compareToIgnoreCase(str2) == 0) { System.out.println("These strings are the same (compareToIgnoreCase())."); }
```  

**<font size="4">contentEquals(StringBuffer sb) / contentEquals(CharSequence cs)</font>**  

> This will also work with a `String`, _not_ just `StringBuffer` or `CharSequence`.  

The contentEquals() method simply checks to see if the strings are _exactly_ the same and gives a boolean as a result:  
```
if (str1.contentEquals(str2)) System.out.println("The condition was true.");
```

**<font size="4">equals(Object anObject)</font>**  

> This will also work with a `String`, _not_ just `Object` (it was overwritten).  

The equals() method simply checks to see if the strings are _exactly_ the same and gives a boolean as a result:  
```
if (str2.equals(objString)) { System.out.println("These strings are the same (equals())."); }
```

_How is this different from contentEquals()_? The difference is `equals()` checks to see if the object is a `String` object, while `contentEquals()` does not check the class type and just the contents.  

**<font size="4">equalsIgnoreCase(String anotherString)</font>**  

If you want to simply operate off a boolean while ignoring the case of the characters, you can use `equalsIgnoreCase()`:  
```
if (str1.equalsIgnoreCase(str3)) System.out.println("The condition was true.");
``` 

**<font size="4">== (Operator)</font>**  

Finally, you can also use the == operator : 
```
if (str1 == str2) { System.out.println("These strings are the same (== operator)."); }
```

!> You may want to avoid using `==`, as this usually just compares memory locations. It _does_ work for primitives - and sometimes for Strings (as it does above) - but you should avoid it otherwise if you can. For Strings, your bread and butter will be `.equals()`, so use that instead of `==` !

## Substrings

<font color="green">Substrings</font> are usually a small snippet from a larger string; for example, the string `Hello` contains multiple substrings:   
* `Hello` 
* `ello` 
* `ell` 
* `l` 
* `lo` 
* `Hell` 
* and many more. 

The substring must contain contiguous letters from the larger string (so for example, `Ho` is _not_ a substring from `Hello`). 

There are a few subclasses to the idea of a substring in Java.  

## Determine Substring Existence  

> This section assumes you know what a [substring](learn_to_code/java/java_basics?id=substrings) is.  

There are a few ways to determine if a substring is contained in a larger string.  

**<font size="4">contains(CharSequence s)</font>**  

> This will also work with a `String`, _not_ just `CharSequence`.  

If you simply want to know if a substring exists in a string, use `contains(CharSequence s)` which returns a boolean. Example: 
```
String myString = "hello!";
String otherString = "llo";
if (myString.contains(otherString)) System.out.println("The condition was true.");
```

**<font size="4">startsWith(String prefix)</font>**  

If you simply want to know if a string starts with a specific substring, use `startsWith(String prefix)` which returns a boolean. Example: 
```
String myString = "hello!";
String otherString = "hel";
if (myString.startsWith(otherString)) System.out.println("The condition was true.");
```

**<font size="4">endsWith(String suffix)</font>**  

If you simply want to know if a string ends with a specific substring, use `endsWith(String suffix)` which returns a boolean. Example: 
```
String myString = "hello!";
String otherString = "o!";
if (myString.endsWith(otherString)) System.out.println("The condition was true.");
```

## Determine Location of Substrings 

> This section assumes you know what a [substring](learn_to_code/java/java_basics?id=substrings) is; also, its helpful to know [if the substring actually exists](learn_to_code/java/java_basics?id=determine-substring-existence).  

Often, its important to know the beginning [index](learn_to_code/java/java_data_structures?id=index) of a [substring](learn_to_code/java/java_basics?id=substrings) in order to [extract a substring](learn_to_code/java/java_basics?id=extracting-substrings). Usually, being able to pick out a known substring is of little worth, but the utility comes into play when you figure out where the position of substring (along with its length) in a larger substring, then you can pick out other strings that surround the substring (using a little math).  

**<font size="4">indexOf(String str)</font>**  

If you wish to find the first position / [index](learn_to_code/java/java_data_structures?id=index) of a substring in a larger string, use `indexOf(String str)`. For example, this will return `1`, as `1` is the first position of the _full_ word `ello`:  
```
String myString = "hello, mello yellow";
int myIndex = myString.indexOf("ello");
System.out.println("Index position: " + myIndex);
```  
* Prints `Index position: 1`  

If we were to make that `myString.indexOf("ello", 1)`, it _would_ have returned the position of that first `ello` (that is to say, if we switched the `2` to a `1`). 

If the substring does not exist at all, `indexOf(String str)` returns `-1`.  

**<font size="4">indexOf(String str, int fromIndex)</font>**  

If you wish to find the position / [index](learn_to_code/java/java_data_structures?id=index) of a substring _after a certain position_, use `indexOf(String str, int fromIndex)`. For example, this will return `8`, as `8` is the position of the _full_ word `ello` starting at position 2 (possition 2 is the first `l` in `hello`, so we are already past the first 'e' so that `ello` does not count):  
```
String myString = "hello, mello yellow";
int myIndex = myString.indexOf("ello", 2);
System.out.println("Index position: " + myIndex);
```  
* Prints `Index position: 8`  

If we were to make that `myString.indexOf("ello", 1)`, it _would_ have returned the position of that first `ello` (that is to say, if we switched the `2` to a `1`). 

If the substring does not exist at all, `indexOf(String str, int fromIndex)` returns `-1`.  

**<font size="4">lastIndexOf(String str)</font>**  

If you wish to find the last position / [index](learn_to_code/java/java_data_structures?id=index) of a substring in a larger string, use `lastIndexOf(String str)`. For example, this will return `14`, as `14` is the position of the substring `ello` in the word `yellow`:  
```
String myString = "hello, mello yellow";
int myIndex = myString.lastIndexOf("ello");
System.out.println("Index position: " + myIndex);
```  

If the substring does not exist at all, `lastIndexOf(String str)` returns `-1`.  

**<font size="4">lastIndexOf(String str, int fromIndex)</font>**  

If you wish to find the last position / [index](learn_to_code/java/java_data_structures?id=index) of a substring _before a certain position_, use `lastIndexOf(String str, int fromIndex)`. For example, this will return `8`, as `8` is the last  position of the _full_ word `ello` before 13 inclusive (position 13 is the `y` in `yellow`, so the `ello` in `yellow` is eliminated and does not count):  
```
String myString = "hello, mello yellow";
int myIndex = myString.lastIndexOf("ello", 13);
System.out.println("Index position: " + myIndex);
```  
* Prints `Index position: 8`  

If we were to make that `myString.lastIndexOf("ello", 14)`, it _would_ have returned the position of that last `ello` (that is to say, if we switched the `13` to a `14`, `lastIndex()` would have returned `14`). 

If the substring does not exist at all, `lastIndexOf(String str, int fromIndex)` returns `-1`.  

## Extracting Substrings

**<font size="4">substring(int beginIndex)</font>**  

The `substring(int beginIndex)` method simply carves out a substring starting with the [index](learn_to_code/java/java_data_structures?id=index) position `beginindex`. For example, the following prints `ello!`, as it starts with position / index 1 (recall that position 0 is actually the first position, so index 1 is actually the second position hence `ello!`): 
```
String myString = "hello!";
String otherString = myString.substring(1);
System.out.println("The selected substring is: " + otherString + ".");
```  

**<font size="4">substring(int beginIndex, int endIndex)</font>**  

The `substring(int beginIndex, int endIndex)` method simply carves out a substring starting with the [index'] position `beginIndex` and ends with (but does _not_ include) position `endIndex`. For example, the following prints `el`, as it starts with position / index 1 (recall that position 0 is actually the first position, so index 1 is actually the second position) and ends with (but does _not_ include) position / index 3:: 
```
String myString = "hello!";
String otherString = myString.substring(1,3);
System.out.println("The selected substring is: " + otherString + ".");
```

!> Note that since `endIndex` does _not_ include the last position, its possible to use up to the length of the string (and we could use either the literal integer `6` here _or_ `myString.length()`); however, if we use an integer _higher_ than `.length()` Java will throw an error.  

## Replacing Substrings  

Its possible to find a [substring](learn_to_code/java/java_basics?id=substrings) in a larger string and replace the substring with something else.  These methods replace substrings with other strings.  

**<font size="4">replace(CharSequence target, CharSequence replacement)</font>**  

> Even though `CharSequence` objects are specified, you can use `String` as well.  

A simple way to replace _all_ matched substrings is with the method `replace(CharSequence target, CharSequence replacement)`. For example, the following code snippet finds all `ello` substrings and replaces them with `xyz`:  
```
String myString = "hello, mello yellow";
String otherString = myString.replace("ello", "xyz");
System.out.println(otherString);
```  

The above starts with `hello, mello yellow` but ends up printing `hxyz, mxyz yxyzw` as all instances of `ello` were replaced with `xyz`.  

**<font size="4">replaceAll(String regex, String replacement)</font>**  

> This method uses a regex - you can read a good tutorial on regexes [here](https://www.vogella.com/tutorials/JavaRegularExpressions/article.html). If you wish to replace all instances of a substring and you do _not_ need a regex, its more efficient to use `replace()` instead of `replaceAll()` - that said, if you need a regex, please continue.  

Another way to replace _all_ matched substrings is with the method `replaceAll(String regex, String replacement)`. Note the difference between `replace()` and `replaceAll()` is `replaceAll()` uses a <font color="purple">regular expression</font>, whereas `replace()` simply takes a `CharSequence` (or `String`). A <font color="purple">regular expression</font> (or <font color="purple">regex</font> for short) is far more powerful; it looks like a string, but regexes are actually a form of code in and of itself. I would strongly suggest you learn about regexes ([here is a good tutorial](https://www.vogella.com/tutorials/JavaRegularExpressions/article.html)), but for now, just know that you can use alphanumeric characters with `replaceAll()` with no issue, but any other character is probably some form of a regex, and you will need to understand what the character in question does in a regex (and even things like `.` have a special meaning).


For example, the following code snippet finds all `ello` substrings and replaces them with `xyz`:  
```
String myString = "hello, mello yellow";
String otherString = myString.replaceAll("el{2}o", "xyz");
System.out.println(otherString);
```  

The above starts with `hello, mello yellow` but ends up printing `hxyz, mxyz yxyzw` as all instances of `ello` were replaced with `xyz`.  

The substring we are searching for is a regular expression: `el{2}o`. The `{2}` means 'the character before `{2}` _must_ be repeated twice (hence, `ll`). 

Here is a more complex example. In this example, there is a space separated (and the number of spaces can vary) list of websites - we wish to carve off the `www.` and the `.com` OR `.net` from the end of the address (we just want to save the middle portion). Here is how that is done:  
```
String myString = "www.vandelayindustries.com      www.costanza.net www.monkscafe.com";
String[] baseWebAddresses = myString.replaceAll("(\\s*www\\.)(.+?)((\\.com)|(\\.net))", "$2 ").strip().split("\\s");
System.out.println("There are " + baseWebAddresses.length + " items in the array, and they are: '" + baseWebAddresses[0] + "', '"  + baseWebAddresses[1] + "', '"  + baseWebAddresses[2] + "'");
```  
* This prints `There are 3 items in the array, and they are: 'vandelayindustries', 'costanza', 'monkscafe'`  

The first thing we gotta know is the parenthesis `()` are _groupings_ in regexes, and the `$` can identify the group when it comes time to note the replacement; so you see a regex with a bunch of parenthesis and then a `$2` in the `replacement` field - this means we want to replace the entire match with whatever the second grouping is; since we are going to group each web address as `(www)` (group 1) `(something)` (group 2) `(.com)` (group 3), we will simply end up with `(something)` and the `www` and `com` portions will be discarded.

The first regex grouping is `(\\s*www\\.)`, which means:
* `\\s` means a whitespace, and a `*` after that means there can be 0 (or more) instances of whatever was before this (so this will match no or multiple whitespace characters). 
* `www` is the literal characters `www`.  
* `\\.` is the literal character `.` (we need to escape it as a dot means something in egex).

The second grouping is `(.+?)`, which means:  
* `.+` means 'match any letter, making sure there is at least one'. 
* The `?` is critical here, as that basically says 'match the minimum'; without this, the match would just go on indefinitely until the last `.com` is encountered (meaning there would be exactly one match for the string, no matter how many web addresses were listed); with the `?`, as _soon_ as something that could match grouping #3 is encountered, the `?` will end the second grouping's boundary and grouping #3 will begin.  

The last grouping is `((\\.com)|(\\.net))`, which means 'the final grouping _must_ either be `.com` _OR_ `.net`. This may be a bit confusing, as there are additional parenthesis in this grouping - do they count in the overall `$` markers? I do not think they do, I think its only the outer groupings that count.  

To round out the `replaceAll()` statement, the `$2` says 'replace the expression found with whatever is in the second grouping'.  Next, we strip off any whitespace from the very beginning and very end of the new string, then we split on whitespace (note the _replacement_ has a single `space` at the end of the `$2` - this was left intentionally so we could `split()`, and the `strip()` will get rid of the last space). Finally you see the base of each website was stored in the array.  


**<font size="4">replaceFirst(String regex, String replacement)</font>**  

> This method uses a regex - you can read a good tutorial on regexes [here](https://www.vogella.com/tutorials/JavaRegularExpressions/article.html). 

`replaceFirst(String regex, String replacement)` works much like `replaceAll(String regex, String replacement)`, but it only replaces the first match it finds. Example:    
```
String myString = "hello, mello yellow";
String otherString = myString.replaceFirst("el{2}o", "xyz");
System.out.println(otherString);
```  

This prints `hxyz, mello yellow`. Only the first instance of `ello` was replaced with `xyz`.  

## Split String into Array

You can split a string into an array of strings using one of the versions of the `split()` method. `split()` takes a <font color="purple">regular expression</font> and splits the larger string up into chunks divided by that substring.  

**<font size="4">split(String regex)</font>**  

> This method uses a regex - you can read a good tutorial on regexes [here](https://www.vogella.com/tutorials/JavaRegularExpressions/article.html). 

You can split on any character (or character sequence) using the `split(String regex)` method; for example, the following splits the string on whitespaces:  
```
String myString = "hello mello yellow";
String[] arrayOfStrings = myString.split("\\s");
System.out.println("The first element in the array is '" + arrayOfStrings[0] + "', and the array length is " + arrayOfStrings.length);
```  

This prints `The first element in the array is 'hello', and the array length is 3`.  The split is done on whitespaces (`\s` denotes a whitespace in regex, but we need a second `\` to act as an escape character so the first one is passed to the regex). We split on all spaces, and there are 3 present in the original string; therefore, the original string is split into an array of 3 `String` elements.  

**<font size="4">split(String regex, int limit)</font>**  

`split(String regex, int limit)` works like `split(String regex)` with a twist: if the array size would be bigger than `int limit`, the strings that would be split are simply left as-is in the last position in the array once that limit is hit.  For example:    
```
String myString = "hello mello yellow";
String[] arrayOfStrings = myString.split("\\s", 2);
System.out.println("The first element in the array is '" + arrayOfStrings[0] + "', the last is '" + arrayOfStrings[1] + "', and the array length is " + arrayOfStrings.length);
```  

This prints:  
```
The first element in the array is 'hello', the last is 'mello yellow', and the array length is 2
```  
* Since the limit was 2 elements on the array, `mello yellow` is stuffed into the last index and not split.  

## Repeating Strings

If you simply need a string repeated X number of times, simply use the `repeat(int count)` method:
```
String myString = "hello, ";
String otherString = myString.repeat(3);
System.out.println(otherString + "...HELLO!");
```  
This prints `hello, hello, hello, ...HELLO!`  

---




# ENUMs

ENUMs are a special data type that should be used when a variable is confined to values in a predefined list; they MUST be one of the values listed.

## Basic ENUM

ENUMs are usually stored in a separate file (much like a [class](learn_to_code/java/java_basics?id=java-classes)). A basic ENUM:
```
public enum Employees {
    RICK, BARBARA, TRACY, 
    CHRIS, ROBERT
}
```

## Referencing ENUM Values

To reference an ENUM value, simply list the ENUM name and then the value you are looking for (using dot notation). Sometimes you need to save the actual value, and other times you need to convert to a string; to convert to a string, use `.toString()`. As an example of how to store a specific value and then convert to a string, the following does just that with `RICK`:
```
Employees singleEmployee;
singleEmployee = Employees.RICK;
System.out.println("The employee is " + singleEmployee.toString());
```
## ENUM Methods and Special Functions

Java's implementation of ENUMs is much better than how other languagues implement ENUMs: 
* Technically they are a class, and as such, you can write different methods (and variables) for the different constant values of the ENUM. 
* ENUMs have an internal `.values()` method, which can cycle through all values of the ENUM.
* ENUMs _do_ have constructors which can set variables for each item in the ENUM, but _you_ cannot interact with or set anything via the constructor.

Here is an example of an ENUM class, `Employees.java`:
```
public enum Employees {
    RICK(1970),
    BARBARA(1955),
    TRACY(1976),
	CHRIS(1969), 
    ROBERT(1958);

    private final int birthyear;

    Employees(int birthyear) {
		/*
		This is the constructor - it takes the different ENUM values and stores their variables (in this case, its each birthyear).
		*/
        this.birthyear = birthyear;
    }

    public double BirthYear() { return birthyear; }

    int YearDelta(int myBirthYear) {
        return birthyear - myBirthYear;
    }

}
```
Note that:
* The ENUM values are listed first, comma separated and terminated with a semicolon.
* Each ENUM value takes advantage of the constructor, which in this case saves its personal birthyear.
 * Reminder: **YOU** cannot interact with the constructor anywhere else in the code.
 * While it is possible to make variables public = and will allow you to set them directly - its usually not recommended.
* To reference a specific ENUMs methods, it will be `ENUMCLASS.ENUM.method()`; for example, to reference `RICK`'s birthyear in code, it would be `Employees.RICK.BirthYear()`.
* You can write your own methods; for example, `YearDelta()` takes a year as an argument and finds the delta between that year and the employee's birthyear.
 * When using this, you have to somehow reference a single ENUM value; see the `for` statement below as an example.

Here is how `Employees` can be implemented in a `main()`:
```
public class Main {

    public static void main(String[] args) {

        int thisYearDelta;
        int myBirthYear;

        myBirthYear = 1970;

        System.out.println("This is how you turn a value into a string; the first name in the ENUM is '" + Employees.RICK.toString() + "'; his birthyear is " + Employees.RICK.BirthYear() + ".");

        for (Employees employee : Employees.values()) {
            thisYearDelta = employee.YearDelta(myBirthYear);
            if (thisYearDelta > 0) {
                System.out.println("You are " + thisYearDelta + " years older than " + employee + ".");

            } else if (thisYearDelta < 0) {
                System.out.println(employee + " is " + -1*thisYearDelta + " years older than you.");
            } else {
                System.out.println("You and " + employee + " are the same age!");
            }
        }

    }
}
```
Note the use of `.values()` in the `for` statement, which cycles through all of the ENUM values.

---

# Operators

## Comparison

The `==` operator _only_ works for primitive variables; for everything else, `==` just checks to see if its the same location in memory. Most code that checks to see if two objects are the same uses the `equals()` method, so its important `equals()` (and `hashCode()`) is overwritten for most (or maybe every) class.

## Standard

| Operator | Description |
| ---      | ---         |
| +      | Plus  |
| -      | Minus        |
| *      | Multiply       |
| /      | Divide       |
| %      | Modulo      |




## Unary Prefix and Postfix Increment Operators

To increment a variable by 1 quickly, use `i++;`.

To decrement a variable by 1 quickly, use `i--;`.

## The Assignment Operators

To increment a variable by a specific number quickly, use `+=`:
```
int a = 5;
a += 10; //is now 15
```

To decrement a variable by a specific number quickly, use `-=`:
```
int a = 15;
a -= 5; //is now 10
```

There are also other assignment operators such as `*=`, `/=`, and `%=`. There are also binary operators which are `&=`, `^=`, `|=`, `<<=`, `>>=`, and `>>>=`.

## Logical Operators

| Operator | Description |
| ---      | ---         |
| ll      | OR    |
| &&      | AND   |
| !   |  Negation |
* Note the `l` above is actuall a pipe `|` but it would not render in docsify.
* Logical operators are shortcut evaluated, which means the rightmost expression is only evaluated if absolutely necessary.

## Bitwise Operators

| Operator | Description |
| ---      | ---         |
| <<      | Left Shift         |
| >>      | Right Shift         |
| >>>      | Unsigned Right Shift         |
| l      | OR         |
| &      | AND         |
| ^      | Exclusive OR         |
| ~      | Negation         |
* Note the `l` above is actuall a pipe `|` but it would not render

---

# Basic Conditionals and Loops

## If Statements
```
if (condition) { }

...

if (condition) { 

} else if (condition) { 

} else {

}
```

## Ternary Operator

The Ternary Operator is a shorthand 'if' statement that has the form <br> `result = (boolean condition) ? (return value if true) : (return value if false)`.

An example would be:
```
minimum = (a < b) ? a : b;
```

Whats cool is you can use function calls in the results; for example:
```
someReturnValue = ifExists(a) ? SomeFunction(a) : 0;
```

You can also use it right in the middle of building a variable!
```
greetingString = "Hello " + (person.IsMan() ? "Mr." : (person.IsMarried() ? "Mrs." : "Miss")) + " " + person.lastName();
```

These can also be chained; for example, this would print `7`:
```
int someNum = ((3<5) ? ((5>3) ? 7 : -1): 0);
System.out.println(someNum);
```

## For Loops

```
for (int i = 0;i<10;1++) { }
```

You can also use multiple variables at once; example:
```
for (int i = 0, j = 10; i < 10; i++, j--) {
	...
}
```

## Foreach Loops

Foreach loops are built for [Arrays](learn_to_code/java/java_data_structures):
```
double[] myDoubleArray = {1.22, 2.3456, 17.578, 99.2};
for (double element: myDoubleArray) {
	System.out.println(element);
}
```
* `element` is used to store the element of the array we are currently inspecting.
 * This `for` loop will loop through all items in the array, and each iteraton will give you a new `element` in the array.
* we are using `double` here, but you must replace that with the primitive (or object) type you are using (as `element` must properly be defined).



## While Loops
```
while (true) { }
```

## DO ... While Loops
```
do { } while(true);
```

# Nulls

In Java, nulls are handled via the `null` keyword; for example, here is some code that showcases how to check to see if an object is null:

```
String[] names = new String[7];
names[0] = "Brent";
names[1] = "Mandi";
names[2] = "Jackson";
names[3] = "Natalia";
names[4] = "Bailey";
names[5] = "Ellie";

for (String element: names) {
	if (element != null) { System.out.println(element); } else { System.out.println("Element is null"); }
}
```
* Since `names` was defined with 7 elements (and we only set 6), the final output will show 'Element is null'.

# Arrays Lists, and HashMaps

Arrays, lists, and hashMaps are collections of [objects](learn_to_code/java/java_classes?id=class-vs-object). These are very useful when you need to store a collection of smaller items. To read more on arrays, lists, and hashMaps, [please see this page on lists, arrays, and hashMaps](/learn_to_code/java/java_data_structures). 

---

# Command Line Arguments

> The example used here was heavily influenced by [a blog on thinkplexx](http://www.thinkplexx.com/blog/simple-apache-commons-cli-example-java-command-line-arguments-parsing).

Command line arguments are important for [headless](learn_to_code/java/java_terms_and_concepts?id=headless) Java applications. You can write a parsing script yourself, but its much better to use one that is already written.

The parsing package I prefer is [Apache Commons CLI](http://commons.apache.org/proper/commons-cli/) (which can be downloaded [here](http://commons.apache.org/proper/commons-cli/download_cli.cgi); the .jar file is located in this .tar.gz file, with the current jar being `commons-cli-1.4.jar`). You will have to place this jar file [on the classpath](learn_to_code/java/java_basics?id=using-jar-files-in-the-classpath), in [IntelliJ's classpath](learn_to_code/java/intellij?id=adding-jars-to-the-classpath), or in the [Maven pom.xml file](learn_to_code/java/maven?id=adding-a-jar-from-a-public-library). The specific JAR file we want from this .tar.gz file is `commons-cli-X.X.jar`, where `X.X` is replaced with the version (currently 1.4).

Here is an example class in file `Cli.java`:
```
import java.util.logging.Level;
import java.util.logging.Logger;

import org.apache.commons.cli.DefaultParser;
import org.apache.commons.cli.CommandLine;
import org.apache.commons.cli.CommandLineParser;
import org.apache.commons.cli.HelpFormatter;
import org.apache.commons.cli.Options;
import org.apache.commons.cli.ParseException;

public class Cli {
    private static final Logger log = Logger.getLogger(Cli.class.getName());
    private String[] args = null;
    private Options options = new Options();
    private CommandLine cmd = null;

    public Cli(String[] args) {

        //store the arguments (will be used later)
        this.args = args;

        //here you will set the options; these are (in this order):
        //  opt:        the short flag, to be used with one dash. Example: -v Victor
        //  longOpt:    the long flag, to be used with two dashes; this is a bit more explicit / verbose than 'opt' but serves the same function. Example: --var Victor
        //  hasArg:     boolean. If 'false', no variable is set (but the option still exists, so action can be taken if appropriate); if 'true', the flag translates to a variable that can be set.
        // description: The description of the variable as it would appear in help.
        options.addOption("h", "help", false, "show help.");
        options.addOption("v", "var", true, "Here you can set parameter .");

    }

    public void parse() {
        //define the parser type
        CommandLineParser parser = new DefaultParser();

        try {
            //try the parse
            this.cmd = parser.parse(options, args);

            //if the option 'h' is set, show the help screen
            if (this.cmd.hasOption("h"))
                help();

            //check to see if the 'v' option is set. Note that 'opt' and 'longOpt' are interchangeable, so if this said 'if (this.cmd.hasOption("var"))'
            //this would work exactly the same
            if (this.cmd.hasOption("v")) {
                //add an INFO line to the log
                log.log(Level.INFO, "Using cli argument -v=" + this.cmd.getOptionValue("v"));
            } else {
                //add a SEVERE line to the log
                log.log(Level.SEVERE, "Missing v option");
                //display the help again, which will also exit immediately afterwards
                help();
            }

        } catch (ParseException e) {
            //if there is a parse error, log it as SEVERE
            log.log(Level.SEVERE, "Failed to parse command line properties", e);
            help();
        }
    }

    public String getV() {
        //simply return the value of v / var (note the interchangeable use of opt and longOpt)
        //if the option does not exist, return null
        String retVal;
        retVal = (this.cmd.hasOption("var")) ? this.cmd.getOptionValue("v") : null;
        return retVal;
    }

    private void help() {
        // This prints out some help
        HelpFormatter formater = new HelpFormatter();

        formater.printHelp("Main", options);

        //exit the program immediately
        System.exit(0);
    }
}
```

Here is some code that calls this class in a `main()`:
```
    public static void main(String[] args) {

        Cli myArgs = new Cli(args);
        myArgs.parse();
        System.out.println("hello, " + myArgs.getV());

    }
```
* This prints `hello, Victor` as I sent the arguments `-v Victor` to the application.

Some notes:
* The `Options` object is required, as it holds the different options (and their individual information).
* The `CommandLine` object (variable name `cmd` above) holds the parsed information
 * The `CommandLineParser` object (variable name `parser` above) is set to some form of a parser; there are many forms, but the default (which utilizes either the single dash or the double dash, i.e. `-v` or `--var`) is `DefaultParser`
   * The `CommandLineParser` will have a `parse(options, args)` function which matches the different options to the arguments, if they exist; once this is completed, the software engineer can access `CommandLineParser`'s `hasOption()` and `getOptionValue()` to interact with the arguments.
 * Each argument returned by `getOptionValue()` will be a [String](learn_to_code/java/java_basics?id=strings); you will have to convert it if you do not want a string.
* There is a `HelpFormatter` class, which helps format the help message (notably, `printHelp()` which takes two arguments: a string (something to identify the help) and an `Options` object.

---

# Java Metadata

## Java Version

To display the version of Java that is currently running, simply use this piece of code:
```
System.out.println( System.getProperty("java.version") );
```

## Other Metadata

There are some instances where you can provide information (above and beyond simply comments) that 

```
    /**
     * This does something
     *
     * @deprecated
     * This method is replaced by...something...else
     *
     * @param nada shows nadayadda
     * @param zip shows zippity-do-dah
     * @return String that shows the true light
     */
    @Deprecated 
    public String getSomething(String nada, String zip) {
        return "This is not the drone you seek";
    }
```
https://www.baeldung.com/java-deprecated
https://beginnersbook.com/2014/09/java-annotations/


---

# Getting OS Information

## Hostname

To get the hostname its:
```
        String system_name;

        try {
            system_name = InetAddress.getLocalHost().getHostName();
        } catch (UnknownHostException e1) {
            system_name = "uhoh";
        }

        System.out.println("This system's name is: " + system_name + "");
```
* Note `getLocalHost()` _requires_ the try/catch block.

---

# Dates and DateTimes

Here is a basic primer on the new classes that manipulate dates and times. The old date-time classes (`java.util.Date`, `java.util.Calendar`, and `java.text.SimpleDateFormat`, as examples) are practically deprecated; moving forward (starting in Java 8), use the `java.time` classes.

## Basic Time Delta

Here is a basic way to time code. Replace the try/catch block with the actual code you are timing:  
```
        long initTime = System.currentTimeMillis();

        try {
            Thread.sleep(3051);
        } catch (Exception e) {

        }

        double deltaTime = (System.currentTimeMillis() - initTime) / (1000.0);
        System.out.println((System.currentTimeMillis() - initTime) + " delta (milliseconds), " + deltaTime + " delta (seconds)");
```

## Getting Current Local Time

Use the `LocalDateTime` class to get the current time, and the `DateTimeFormatter` class to format the time:

```
import java.time.format.DateTimeFormatter;
import java.time.LocalDateTime;

...
...

        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss");
        LocalDateTime now = LocalDateTime.now();
        System.out.println(dtf.format(now));
		
```
* The pattern `yyyy/MM/dd HH:mm:ss` can be set in any combination by you (see [below](learn_to_code/java/java_basics?id=datetimeformatter-patterns)).  

## DateTimeFormatter Patterns

The following are popular `DateTimeFormatter` patterns:

| Symbol | Meaning | Notes | 
| --- | --- | --- | 
| `y` or `Y` | Year | 1, 3, or 4 of these in a row yield a year in `YYYY` format; two gives `YY` |
| `M` | Month (Number) | `MM` or `M` gives the number of the month, `MMM` gives the 3 character code, and `MMMM` gives the full Month's name. | 
| `d` | Day (Number) | `d` gives the date (number), `dd` simply puts a zero in front of numbers < 10. | 
| `Q` | Quarter of year (1 through 4) | 1 `Q` gives a single number, `QQ` gives a zero and then the quarter, `QQ` will print a Q before the quarter. | 
| `H` | Hour (24-Hour time) | `H` gives the hour, `HH` ensures hours < 10 have a zero in front. | 
| `h` | Hour (12-Hour time) | `h` gives the hour, `hh` ensures hours < 10 have a zero in front. | 
| `m` | Minutes | `m` gives the minute, `mm` ensures minutes < 10 have a zero in front. | 
| `s` | Seconds | `s` gives the second, `ss` ensures seconds < 10 have a zero in front. | 
| `w` | Week Number (Year) | `w` gives the current week of the year, `ww` ensures the weeks < 10 have a zero in front. | 
| `W` | Week Number (Month) | `W` gives the current week number of the current month. | 
| `e` | Day of Week | `e` gives the current numeric day of the week (Sunday = 1), `ee` ensures the weeks < 10 have a zero in front, `eee` gives the three character representation of the day (i.e. Tue), `eeee` gives the full day name. | 
| `a` | AM or PM | | 
| `S` | Fraction of a second | This can be pretty precise. I gave up on `SSSSSSS` | 
| `A` | Millisecond of the day. | Use as many `A`'s as necessary. 
| `n` | Nano of second. | |
| `N` | Nano of day. | |
| `z` | Time zone. | `z`, `zz`, and `zzz` work here - one gives an abbreviated and one gives the full name (not sure which as my example was in `GMT`). Do **not** use this for times that do not have a zone (like `LocalDateTime`). | 
| `x` | Time zone offset. | `x` gives `+/-XX`, `xx` gives `+/-XX00`, `xxx` gives `+/-XX:00`.  Do **not** use this for times that do not have a zone (like `LocalDateTime`). | 

## Setting LocalDateTime / ZonedDateTime

`LocalDateTime` and `ZonedDateTime` are almost interchangeable - the only major difference being you will use `ZonedDateTime` for times you want to use a timezone (`LocalDateTime` does not support timezones).

There are multiple ways to set `LocalDateTime` and `ZonedDateTime`. The first just sets it to the current time:
```
LocalDateTime currTime = LocalDateTime.now();
ZonedDateTime currTime = ZonedDateTime.now();//uses local time
ZonedDateTime currTime = ZonedDateTime.now(ZoneId.of("GMT"));//specific timezone
```
* The first and second lines are examples of setting each type to the current time.
* The last line shows how you can set a `ZonedDateTime` variable to the current time using the specified timezone (in this case, GMT).
* Note the use of the [ZoneId](learn_to_code/java/java_basics?id=zoneid-class) class.

You can also set the time to `now()` but alter it by chaining functions off of the `now()`; for example, 
```
LocalDateTime currTime = LocalDateTime.now().withYear(2010).withMonth(12).withDayOfMonth(8).withHour(8).withMinute(0).withSecond(0).withNano(0);
ZonedDateTime  currTime = ZonedDateTime.now().withYear(2010).withMonth(12).withDayOfMonth(8).withHour(8).withMinute(0).withSecond(0).withNano(0);
```
* Both of these use the `now()` as a base, and set each individual component of the time.

The above is a bit clunky if you intend on setting the entire time - it may be best to use the `of()` function instead:
```
LocalDateTime currTime = LocalDateTime.of(2010, 12, 8, 8, 0, 0, 0);
ZonedDateTime currTime = ZonedDateTime.of(2010, 12, 8, 16, 0, 0, 0, ZoneId.of("GMT"));
```
* Sets the year, month, day, hour, minute, second, and nanosecond.
* In the case of ZonedDateTime, the timezone is set with the [ZoneId](learn_to_code/java/java_basics?id=zoneid-class) class.

## ZoneId Class

The `ZoneId` class (from `java.time.ZoneId`) helps set timezones for `ZonedDateTime`.  

To list all timezones available you can use `ZoneId.getAvailableZoneIds()`; that said, the predominant ones for the US are:
```
GMT, UTC, US/Eastern, US/Central, US/Mountain, US/Pacific, US/Alaska, US/Arizona, US/Hawaii, 
Pacific/Guam, America/Puerto_Rico, US/Samoa, Pacific/Samoa, America/Barbados
```
* `America/Barbados` is used for the Virgin Islands.

In most cases the timezone has to be converted from a string; this is done with the `of()` method of `ZoneId`. So, for example, `ZoneId.of("US/Eastern")` would return the timezone for `US/Eastern`.

Finally, 'ZoneId.systemDefault()' gets the default (if you wish to use this instead).

## Adding and Subtracting DateTime

There are many methods to add and subtract time quickly; for example, to add one month its:
```
ZonedDateTime currTime = ZonedDateTime.now(ZoneId.of("GMT"));
currTime = currTime.plusMonths(1);
```
* Note this is _not_ done in-place.

The above uses `plusMonths()` but there are all kinds of 'plus' methods (and 'minus' methods too).

There are also base `plus()` and `minus()` methods that take a long and a unit; to use this, you will need the ENUMs from `java.time.temporal.ChronoUnit`. For example, to add one day (using milliseconds) it would be:
```
ZonedDateTime currTime = ZonedDateTime.now(ZoneId.of("GMT"));
currTime = currTime.plus(86400000, ChronoUnit.MILLIS);
```
* There is also `ChronoUnit.MONTHS`, `ChronoUnit.DAYS`, `ChronoUnit.HOURS`, etc.
* Note this is _not_ done in-place.

## Adding and Subtracting Epoch Milliseconds

> Unfortunately, milliseconds from the Epoch (Jan 1st, 1970) is a very large number; the normal `long` gives us problems, as typing a literal long in the code will error out. Because of this, we must use the class `BigInteger` (`java.math.BigInteger`) instead of a `long`.

Many times we have to deal with milliseconds from the Epoch (Jan 1st, 1970); these can be handled without too much of a headache.

To convert to milliseconds from the Epoch its:
```
ZonedDateTime currTime = ZonedDateTime.now(ZoneId.of("GMT"));//specific timezone
long long_millis = currTime.toInstant().toEpochMilli();
```
* `long_millis` is a long that holds the value.
* `LocalDateTime` does **not** work here - it _must_ be a `ZonedDateTime` class type.

To convert back to a time, we are forced to use the `java.time.Instant` class:
```
Instant inst = Instant.ofEpochMilli(long_millis);
currTime = ZonedDateTime.ofInstant(inst, ZoneId.of("GMT"));
```
* Note the timezone is unknown from the long number alone - you _must_ supply it.
* `currTime` is now of type `ZonedDateTime`.
 * `LocalDateTime` does **not** work here - it _must_ be a `ZonedDateTime` class type.

To subtract (we could have just as easily used add) we use two instances of `BigInteger`:
```
BigInteger millis = new BigInteger(String.valueOf(long_millis));

BigInteger secondNum = new BigInteger("86400000");
millis = millis.subtract(secondNum);

currTime = ZonedDateTime.ofInstant(Instant.ofEpochMilli(millis.longValue()), ZoneId.of("GMT"));
System.out.println("Base date (Readable): " + dtf.format(currTime));
```
* A new `BigInteger` _must_ be initialized with a `String` and not a `long`, so we convert the long to a String in the first line.
* Unfortunately, BigInteger does NOT add / subtract well with other datatypes - you have to add / subtract it using another instance of the BigInteger class (as we have done with `secondNum`).
 * Since we will be subtracting times, we need a second number to add usingBigInt - the first instance (i.e. `millis`) will subtract one day (86400000 milliseconds is a day) from what is stored in `currTime`.
* The last two lines convert the `BigInteger` to a `long`, which is then converted to a `ZonedDateTime`; immediately after this, the readable datetime is printed to the screen.

## Comparing DateTimes

`ZonedDateTime` objects can use the `compareTo()` method to see if they are greater than (`compareTo()` returns a positive number), less than (`compareTo()` returns a negative number), or equal to (`compareTo()` returns 0) another date.

```
ZonedDateTime currTime = ZonedDateTime.of(2010, 12, 8, 16, 0, 0, 0, ZoneId.of("GMT"));

if(currTime.compareTo(ZonedDateTime.now(ZoneId.of("GMT"))) > 0) {
	System.out.println("currTime is greater than now()");

} else if(currTime.compareTo(ZonedDateTime.now(ZoneId.of("GMT"))) < 0) {
	System.out.println("currTime is less than now()");
} else {
	System.out.println("currTime is exactly now()");
}
```
* The timezones do not actually have to be the same above.

> If both timezones are equal, the functions `isAfter()`, `isBefore()`, and `isEqual()` can also be used in place of `compareTo()`.

## Getting Time To Next Period 

> [The Epoch Converter website](https://www.epochconverter.com/) is very helpful here.  

In many statistic collection strategies, there exists a concept of a <font color="green">period</font>; a <font color="green">period</font> is a unit of chunked time (and its usually an hour or less). For example, if we wanted our base <font color="green">period</font> to be 15 minutes, there would be 4 <font color="green">period</font>s each hour:
* `00:00` to `15:00` (the first period in the hour) 
* `15:00` to `30:00` 
* `30:00` to `45:00` 
* `45:00` to `00:00 (next hour start)` (the last period in the hour) 

Usually, these two things are true about a period: 
* The name associated with a <font color="green">period</font> is usually the datetime of the beginning of the period (so for the 15 minute period that encompasses the timeframe `2019-02-02 01:15:00` to `2019-02-02 01:30:00` would be known as the `2019-02-02 01:15:00` time period).  
* They usually neatly align with the hour (so if we were using 15 minute <font color="green">period</font>s, they would start _exactly_ when the hour started (or a 15 minute derivative) and not shifted).  

This 'not shifted' property can be difficult, as sometimes we need to start a timer to let us know when the next timer will begin (say, to kick off a process that dumps the statistics to a database) - but if we start that timer at some random time that is not at the top of the natural time a <font color="green">period</font> would start, we need a way to find the time between the current time and the next natural <font color="green">period</font> start time to kick off that process at _exactly_ the right natural <font color="green">period</font> start time.  

We need to find the exact milliseconds it will take to align the schedule with our chosen time period; once we do, we use this number as our first 'launch' and we will be aligned with the natural <font color="green">period</font> time. To do this, we:
* `(A)` take the current time and divide by 86400000 (number of milliseconds in a day) – this gives us the midnight of today, exactly, if converted from the epoch time
* `(B)` take the current time mod 86400000, which gives us the remaining milliseconds in today (after midnight)
* `(C)` take (floor) `(B)` and divide by 3600000 (the number of milliseconds in an hour), which gives us the number of hours after midnight as a result
* `(D)` take `(B)` mod 3600000, which gives us the remainder of milliseconds in the current hour
* `(E)` take (floor) `(D)` and divide by 60000 (the number of milliseconds in an hour), which gives us the exact minutes in the hour
* `(F)` take `(D)` mod 60000, which gives us the remainder of seconds in the current minute

Once we have `(F)` remainder of minutes, we can get a baseline: we take `(F)` and divide by the number of minutes that 1) evenly divide into 60 and 2) we are using as a time base (in our case the base is 15 minutes - so we will use 15 as a default, but we will reference as X); this gives us how many 'units' of time has passed (so if the minutes were 52, 3\*15 = 45 so 3 units have passed). We add one to this number (which gives us the next 'unit' bracket) and then multiply that by 1000\*60\*X, then subtract `(D)` and you will get the time (in milliseconds) it will take to reach the next <font color="green">period</font> 'step' (which we will use as the initial launching time).

The code below is an example of how to do this; it assumes a period (`myBase`, which is a 15 minute <font color="green">period</font> in the code below) is A) less than or equal to an hour and B) neatly divides into an hour (so a 1 minute period, 5 minute period, 10 minute period, 15 min period, 20 minute period, 30 minute period, or 60 minute period would all work well).  

```
package com.wagenseller;

public class Main {

    public static void main(String[] args) {

        int myBase = 15;
        long current = System.currentTimeMillis();//1575600850810l
        long baseDayAfterEpoch = (long)Math.floor((double)current/(double)86400000l); // (A)
        long remainderOfDay = current % 86400000l; // (B)
        long baseHour = (long)Math.floor((double)remainderOfDay/(double)3600000l); // (C)
        int remainderOfHour = (int)(remainderOfDay % 3600000); // (D)
        int baseMinute = (int)Math.floor((double)remainderOfHour/(double)60000); // (E)
        int remainderOfMinute = remainderOfHour % 60000; // (F)
        int baseX = (int)Math.floor((double)baseMinute/(double)myBase);

        //this gives the datetime, but with the minutes, seconds, and milliseocnds stripped out
        long reconstructExactDateHour = (baseDayAfterEpoch*86400000l) + (baseHour*60*60*1000);

        int toNext = ((baseX+1)*1000*60*myBase) - remainderOfHour;
        System.out.println("Base Epoch Hour (milliseconds): " + reconstructExactDateHour);
        System.out.println("Use this to find base day:  "  + baseDayAfterEpoch + " remainder: " + remainderOfDay + " baseHour: " + baseHour + "  remainderOfHour: " + remainderOfHour + " baseMinute: " + baseMinute + "  remainderOfMinute: " + remainderOfMinute);
        System.out.println("Number of Periods Passed This Hour (not including current period):  "  + baseX + "   milliseconds to next period: " + toNext);
        System.out.println("Next Period Starts at this Epoch Timestamp:  "  + (current + toNext));

    }
}

```


## Full DateTime Example

```
import java.math.BigInteger;
import java.time.Instant;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;
import java.time.temporal.ChronoUnit;
import java.time.ZoneId;
import java.time.LocalDateTime;

public class DateTimeExample {

    private static final Logger logger   = LoggerFactory.getLogger("wagdaemon");

    public static void main( String[] args )
    {

		//Many different ways to set the time are below, using LocalDateTime and ZonedDateTime
        //LocalDateTime currTime = LocalDateTime.now().withYear(2010).withMonth(12).withDayOfMonth(8).withHour(8).withMinute(0).withSecond(0).withNano(0);
        //ZonedDateTime  currTime = ZonedDateTime.now().withYear(2010).withMonth(12).withDayOfMonth(8).withHour(8).withMinute(0).withSecond(0).withNano(0);
        //LocalDateTime currTime = LocalDateTime.of(2010, 12, 8, 8, 0, 0, 0);
        //ZonedDateTime currTime = ZonedDateTime.now();//uses local time
        //ZonedDateTime currTime = ZonedDateTime.now(ZoneId.of("GMT"));//specific timezone
        ZonedDateTime currTime = ZonedDateTime.of(2010, 12, 8, 16, 0, 0, 0, ZoneId.of("GMT"));

        ZonedDateTime currTime = ZonedDateTime.of(2010, 12, 8, 16, 0, 0, 0, ZoneId.of("GMT"));

        if(currTime.compareTo(ZonedDateTime.now(ZoneId.of("GMT"))) > 0) {
            System.out.println("currTime is greater than now()");

        } else if(currTime.compareTo(ZonedDateTime.now(ZoneId.of("GMT"))) < 0) {
            System.out.println("currTime is less than now()");
        } else {
            System.out.println("currTime is exactly now()");
        }
		
        //converting to milliseconds since 1970-01-01 ONLY works with ZonedDateTime, NOT LocalDateTime
        //'System.currentTimeMillis()' was the default use but that only works if you are using the JVM timezone, and thats only for the current time
        long long_millis = currTime.toInstant().toEpochMilli();

        //show how the time in milliseconds since the Epoch will work
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss z  ");
        System.out.println("Time test: " + dtf.format(currTime));
        System.out.println("Time test (milliseconds): " + long_millis);

        //re-buld from Epoch milliseconds
        Instant inst = Instant.ofEpochMilli(long_millis);
        currTime = ZonedDateTime.ofInstant(inst, ZoneId.of("GMT"));
        System.out.println("Re-built time: " + dtf.format(currTime));

        //get base of the date used in milliseconds; note BigInteger must use a string so we convert it
        BigInteger millis = new BigInteger(String.valueOf(long_millis));

        //Unfortunately, BigInteger does NOT add well with other datatypes - you have to add it using the BigInteger class.
        //Since we will be adding times, we need a second number to add usingBigInt - the first instance will add one day (86400000 milliseconds is a day) to the current date.
        BigInteger secondNum = new BigInteger("86400000");
        millis = millis.subtract(secondNum);

        currTime = ZonedDateTime.ofInstant(Instant.ofEpochMilli(millis.longValue()), ZoneId.of("GMT"));
        System.out.println("Base date (Readable): " + dtf.format(currTime));

        //add exactly 5 minutes to the previous time - this will be the start time (note BigInteger only accepts strings)
        secondNum = new BigInteger(String.valueOf(60000*5));
        millis = millis.add(secondNum);
        System.out.println("start: " + millis.longValue());

        currTime = ZonedDateTime.ofInstant(Instant.ofEpochMilli(millis.longValue()), ZoneId.of("GMT"));
        System.out.println("Start (Readable): " + dtf.format(currTime));

        //add exactly 3 minutes to the previous time - this will be the end time (note BigInteger only accepts strings)
        secondNum = new BigInteger(String.valueOf(60000*3));
        millis = millis.add(secondNum);
        System.out.println("end: " + millis.longValue());

        currTime = ZonedDateTime.ofInstant(Instant.ofEpochMilli(millis.longValue()), ZoneId.of("GMT"));
        System.out.println("End (Readable): " + dtf.format(currTime));

        currTime = currTime.plus(86400000, ChronoUnit.MILLIS);
        System.out.println("Add 1 day (using Milliseconds): " + dtf.format(currTime));

        currTime = currTime.minus(1, ChronoUnit.MONTHS);
        System.out.println("Subtract 1 month: " + dtf.format(currTime));
    }
}

```

---

# Lambda Function

> I read about lambdas [here](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/Lambda-QuickStart/index.html); another good reference I used is [here](https://beginnersbook.com/2017/10/java-lambda-expressions-tutorial-with-examples/).

A <font color="green">lambda function</font> is an 'anonymous' function (or even class) that you can create in-line / on the fly. This can be advantageous - especially for classes - as this eliminates the need to make (in some cases) multiple files for small classes that can be handled in a few lines of code. A <font color="green">lambda function</font> are usually used when an anonymous class will be used that have only one method.

Usually, lambda functions are defined as such:

```
parameters -> function
```

## General Example

There seem to be multiple ways to implement a lambda function, but this i sthe most general way to do it. Here is a general example of a lambda using variables:
```
interface LambdaExampleWithParameters {
    int addStuff(int a, int b, int c);
}

public class Main {

    public static void main(String[] args) {

        // Lambda definition
        LambdaExampleWithParameters variableNameHere = (a, b, c) -> {
            int d;
            d = a + b + c;
            return d;
        };


        int aa = 2, bb = 5, cc = 3;
        int sumTotal = variableNameHere.addStuff(aa,bb,cc);

        System.out.println("The results: " + sumTotal);
    }
}
```
* The `interface`
 * It seems you cannot add more than one function definition to the `interface`; if you add more than one it will not work.
 * The function definition could have been declared public (as in, `public int addStuff(int a, int b, int c)`), but it is assumed to be public anyway.
* We define an interface, then create a variable that utilizes that interface (in our case `variableNameHere`) and we then write our own function internals.
 * Later on, we can treat the sole function in the interface as we would a method of a class (which is why `variableNameHere.addStuff(aa,bb,cc)` works above).

!> This is a trivial example, but there are times where an interface (and their known parameters) is commonplace, but _you_ have to write the internals of the function; later, that function will be used to perform other tasks. In these instances, the internals of the function are not known, but the parameters _and_ the output _are_ known. This is the power of the lambda.

## Using a foreach Loop

You can also use a lambda in a `forEach` loop that is associated with a list:
```
import java.util.*;

interface LambdaExampleWithParameters {
    String addLastName(String fname);
}

public class Main {

    public static void main(String[] args) {

        // Lambda definition (similar to the general example above)
        LambdaExampleWithParameters seriouslyAddLastName = (pname) -> {
            String finalName;
            finalName = pname + " " + "Johnson";
            return finalName;
        };


        List<String> myList=new ArrayList<String>();
        myList.add("Ricky");
        myList.add("Julian");
        myList.add("Bubbles");

        myList.forEach(
                //lambda expression here
                (namesQ) -> {
                    namesQ = seriouslyAddLastName.addLastName(namesQ);
                    System.out.println(namesQ);

                }

        );
    }
}
```
* This is actually using _two_ lambdas:
 * The one that is referenced in `seriouslyAddLastName.addLastName(namesQ)`, which was covered [in the general example](learn_to_code/java/java_basics?id=general-example).
 * The one that is `(namesQ) -> { ... }`
   * This is the one we will focus on in this example.
* It is assumed that whatever single parameter is listed in `(...)` will be the next object in the list.
* Note that, in this usage, `namesQ` is read-only
 
## Runnable Lambda

This example utilizes a <font color="green">runnable lambda</font>, which does not need an `interface` pre-built. I could not get this to accept any parameters and try as I may, I could not find an example of a <font color="green">runnable lambda</font> that did utilize parameters; most examples were very simplistic, but the ones that were a bit mroe complex exclusively tied in launching threads with the <font color="green">runnable lambda</font> - so the main usage of these may be launching threads. A basic example:
```
public class Main {

    public static void main(String[] args) {

        System.out.println("=== RunnableTest ===");

        // Anonymous Runnable
        Runnable r1 = new Runnable(){

            @Override
            public void run(){
                System.out.println("Hello world one!");
            }
        };

        // Lambda Runnable
        Runnable r2 = () -> System.out.println("Hello world two!");

        // Run em!
        r1.run();
        r2.run();
    }

}
```
* There are two anonymous runnable lambdas above
 * `r1` is the first lambda and is defined sort of like a class with one method.
   * The sole method is named `run()`
 * `r2` is the second lambda function, and it is implied with the syntax `() -> -> ...`.
   * If there is only one line, the `{ ... }` can be omitted, otherwise you would need the braces a la `() -> -> { ... }`
* The `run()` method is critical; `r1` specifies it, but `r2` implies it.
 * It seems other classes that can use these - notably threads - use `run()` to launch.
 
Example 2 (with threads):
```
import java.util.*;

public class Main {

    public static void main(String[] args) {

        List<Thread> myThreads=new ArrayList<Thread>();
        final int threadCount = 10;

        // Anonymous Runnable
        Runnable r1 = new Runnable(){

            //@Override
            public void run(){
                System.out.println("Inside Thread: " + Thread.currentThread().getName());
            }
        };

        System.out.println("Main : " + Thread.currentThread().getName());

        System.out.println("Creating Threads...");
        for (int i=0; i<threadCount; i++) {
            myThreads.add(new Thread(r1));
        }

        System.out.println("Starting Threads...");
        myThreads.forEach(
                //lambda expression here
                (deckThread) -> {
                    deckThread.start();
                } );
    }
}
```
* Two lambda functions are used here
 * the anonymous runnable `r1`
 * the lambda function in the `forEach()`
* This works because the `Thread<>` class knows to look for the `run()` method; this is why having this method is important.

---

# Wait

Its possible to wait in Java by using `Thread.sleep(milliseconds)`; example: 
```
	try {
		Thread.sleep(3000);
	} catch (Exception e) {

	}
```  


The above waits for 3 seconds (3000 milliseconds). Just note that `Thread.sleep(milliseconds)` _must_ be in a try/catch block.

!> This will make the _entire_ thread sleep; if this is run on the thread that is controlling other threads, the _entire program_ will sleep. Be careful!  

---

# Streams

<font color="green">Streams</font> in Java have nothing to do with I/O: <font color="green">streams</font> are pipelines of operations that can run sequentially or in parallel.

---

# Code Example: StringSort

This is the 'App.java' file:

```
public class App {
	public static void main(String[] args) {
		var actualNames = new String[]{
			"Johnson", "Wilson",
			"Wilkinson", "Abraham", "Dagobert"};
		Sort.sort(actualNames);
		for (final String name : actualNames) {
			System.out.println(name);
		}
	}
}
```

This is the 'Sort.java' file:
```
package packt.java11.example.stringsort;

public class Sort {

	public static void sort(String[] names) {
		var n = names.length;
		while (n > 1) {
			for (var j = 0; j < n - 1; j++) {
				if (names[j].compareTo(names[j + 1]) > 0) {
					final var tmp = names[j + 1];
					names[j + 1] = names[j];
					names[j] = tmp;
				}
			}
			n--;
		}
	}
}
```