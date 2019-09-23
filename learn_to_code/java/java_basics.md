# Learning To Code: Java

# Before We Begin

This documentation is meant for those that have at least a basic grasp of how to write general code (so you understand what 'compile' and 'run' means, you understand 'if' conditionals, 'while' and 'for' loops, etc). This documentation is to teach people in this category the general concepts of Java first, and then we will start writing some code. <br> <br>

Java has a basic formula for writing an application: you will create a [basic Java file](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program) that has [a class](learn_to_code/java/java_basics?id=java-classes); you will [compile](learn_to_code/java/java_basics?id=basic-compiling) your code, and then [run](learn_to_code/java/java_basics?id=basic-script-running) it. You can re-use code with [JAR Files](learn_to_code/java/java_basics?id=jar-files) if you wish.

## Why I Do This

I find that I have to write things down, in a format I understand, in order for me to fully understand a topic; therefore I take notes on subjects I try to learn. These notes are an artifact of that line of thinking.

## My Note Sources

My main note sources are various code examples I have seen on the web, [stackoverflow](https://stackoverflow.com/), and the Packt book [Java Projects (Second Edition) by Peter Verhas](https://www.packtpub.com/application-development/java-projects-second-edition).

## Java Version

This document was made for Java version 11.

## Installing Java

I wrote how to install java either [with the default jre](ubuntu/server_build?id=install-java-jre) or [from Oracle](ubuntu/server_build?id=installing-java-from-oracle).

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
 * `main()` must be [static](learn_to_code/java/java_basics?id=defining-static-methods-in-classes); unfortunately, if a method is `static` it cannot call any non-static methods, so the initial class that holds the `main()` must be pretty much bare-bones, only call other static methods in itself, _or_ create a copy of itself, sending a new instantiated object of itself to its own starting method.
   * For example, if you had a `start()` method that handled the contents of `main()` above you could simply write `new DragonBorn().start(args);` in your `main()` method, which creates an instance of itself that _can_ call non-static methods.
* The method `DragonBorn()` is a [constructor](learn_to_code/java/java_basics?id=class-constructors).
* The variable `dragonSoulCount` - and its associated methods `increaseSoulsTaken()` and `showSoulCountTaken()` - are a bit advanced, but you can read a bit more about them [here](learn_to_code/java/java_basics?id=defining-static-variables-in-classes).

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

## Description of a Class

Java classes are a bit overwhelming but the truth is, they are fairly straightforward. A class can be considered a grouping of things; so for example, there could be a 'building' class/group, and members of this group are things like house, skyscraper, shed, outhouse, etc. The idea of a 'Building' is a bit vague - and at first, classes are a bit vague - but things that are _defined_ as a building - a house, for example - is a bit more granular.

Early in this document I defined a [DragonBorn class](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program); this was named from a concept in one of my favorite video games, [The Elder Scrolls V: Skyrim](https://en.wikipedia.org/wiki/The_Elder_Scrolls_V:_Skyrim). The [Dragonborn](http://elderscrolls.wikia.com/wiki/Dragonborn_%28Lore%29) refers to someone born with the blood and soul of a dragon, but the body of a mortal human; there are multiple people who were 'Dragonborn' throughout the history of Skyrim.

So, 'Dragonborn' is a class, but any _individual_ instance of a 'Dragonborn' is not a class, but a specific person (such as the main character in Skyrim). 

## Class vs Object

You must know the difference between a class an an object; a class is a grouping, but an object is an instance of that class; that is to say, class:object as Dragonborn:(main character in Skyrim).

Objects are declared with the `new` keyword.

If you still need help on understandng the differences between a class and an object, [this handy website](https://www.javatpoint.com/difference-between-object-and-class) sums it up nicely:

| Object | Class |
|    ---   |   --- | 
| Object is an instance of a class. | Class is a blueprint or template from which objects are created. |
| Object is a real world entity such as pen, laptop, mobile, bed, keyboard, mouse, chair etc. | Class is a group of similar objects. |
| Object is a physical entity. | Class is a logical entity. |
| Object is created through new keyword mainly <br> e.g. Student s1=new Student(); | Class is declared using class keyword <br> e.g. class Student{} |
| Object is created many times as per requirement. | Class is declared once. |
| Object allocates memory when it is created. | Class doesn't allocated memory when it is created. |
| There are many ways to create object in java such as new keyword, newInstance() method, clone() method, factory method and deserialization. | There is only one way to define class in java using clas |

## Defining Scopes in Classes

Scope, in computer science terms, can be boiled down to 'what parts of the code have visibility to this entity'; practically, we must worry about the scope of several things, usually functions or variables. For example, some variables can only be seen inside of specific classes (or functions) and cannot be referenced outside of that class or function; other times, variables are _global_ and can be referenced anywhere in the code.

When class functions are defined as _public_, those functions can be referenced from entities outside of the class; when declared _private_, only other functions inside the class itself can access private functions. Using our [DragonBorn class](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program) class, the DragonBorn class function `public static void witnessDragonBorn()` is private; if there exists some other Java code that uses the DragonBorn class, it could create a DragonBorn object and call this function directly:
```
...
DragonBorn heroOfSkyrim = new DragonBorn();
heroOfSkyrim.witnessDragonBorn();
...
```

This would print "You.....you took it's soul."

## Defining Static Methods in Classes

We see the word _static_ in the defintion of the witnessDragonBorn() function: this means that we dont necessairly have to declare an object, we can just call this function from the class directly;  this means that instead of writing 
```
...
DragonBorn heroOfSkyrim = new DragonBorn();
heroOfSkyrim.witnessDragonBorn();
...
```

...we could simply write:
```
...
DragonBorn.witnessDragonBorn();
...
```

## Defining Static Variables in Classes

Methods can be static (see above), but variables can also be static. When a variable is static, _every_ instance of that class will share that variable.

Consider the [DragonBorn](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program) - it has two variables, `dragonSoulCount` and `level`. If we were to create two instances of the `DragonBorn` class and then call `increaseSoulsTaken()` on each, the variable `dragonSoulCount`for _both_ instances would be 2; so for example, if we put this code in `main()`:
```
	DragonBorn heroOfSkyrim = new DragonBorn();
	DragonBorn anotherHeroOfSkyrim = new DragonBorn();
	
	heroOfSkyrim.increaseSoulsTaken();
	anotherHeroOfSkyrim.increaseSoulsTaken();
	
	System.out.println("The number of souls taken for heroOfSkyrim is " + heroOfSkyrim.showSoulCountTaken() + " and the number of souls taken for anotherHeroOfSkyrim is " + anotherHeroOfSkyrim.showSoulCountTaken() + ".");
```

This would print "The number of souls taken for heroOfSkyrim is 2 and the number of souls taken for anotherHeroOfSkyrim is 2.", because the variable `dragonSoulCount` is shared by both `heroOfSkyrim` and `anotherHeroOfSkyrim`.

You will notice `dragonSoulCount` is not an `int`, its an `AtomicInteger`. This is because `int` is not <font color="purple">thread safe</font>. <font color="purple">Threads</font> are a way to run code in parallel (so basically you run two batches of code concurrently) - if you do not understand threading yet you will not have to use this (as you will know when using a thread), but you should know of their existance. <font color="purple">Thread safe</font> means since this is a shared variable, if multiple threads are using this class and they all try to use `dragonSoulCount` at the same time you may get an incorrect result.

When using static variables in a class, you should always make sure you are using a <font color="purple">thread safe</font> variable; [primitive variables](learn_to_code/java/java_basics?id=primitive-variables) - such as `int`, `double`, etc - are _not_ <font color="purple">thread safe</font>. Here are primitive variables and what yo ushould use instead if using a `static` variable:

| Primitive Type | Use Instead | 
| --- | --- | 
| int | AtomicInteger | 
| long | AtomicLong | 
| boolean | AtomicBoolean | 

Note that there are no atomic classes for `byte`, `short`, `char`, `float`, and `double`; the makers of Java did not want to spend the time on these and officially tell you to use one of the above and convert them. For `float`, the maintainers of Java suggest using the conversions `Float.floatToIntBits` and `Float.intBitstoFloat`; for `double`, they suggest using `Double.doubleToLongBits` and `Double.longBitsToDouble`.

## Class Constructors

Each time we wish to use a class (by creating an object), we must do something like so:
```
Sort sorter = new Sort();
```

This says 'we are creating a new object called 'sorter', it will be of type 'Sort', and we wish to create this now (with `new Sort()`)'. Each time this is done, the class **constructor** is called, which is a function that is called when an object is created. Technically, a constructor does not return a value because it returns the object itself.

The contstructor may do nothing at all, but oftentimes it helps initialize the object. There can be multiple constructors for the same class, each with a different parameter list; or, there may be no constructor at all, at which point Java will make one for the class (a bare bones constructor).

You can make a constructor like so:
```
    //this is a constructor, which is used to initialize a School Object
    //
    School(String name, String address, String phone) {
        
        this.address = address;
        this.name = name;
        this.phone = phone;
    }
```
* This is a `School` class - constructors should be named the same as the class itself.
* Note there is NO type, not even void.
* Note there is NO scope - it is assumed t obe public.
* The `this` references class variables defined in itself; the variable that does not use the 'this' comes from the parameters. This is how you can set the class variables from parameters.

You can also have multiple constructors in the same class, _provided_ they do **not** use the same configuration of parameter types. An example:
```
    School(String name, String address, String phone) {
        
        this.address = address;
        this.name = name;
        this.phone = phone;
    }
	
    School(String name) {
        this.name = name;
    }
```
* The second constructor is OK because it does not use the same types in the same order; if we were to add a third constructor `School(String address, String name, String phone)` this would create an error as its another `School(String, String, String)` constructor.

## Overriding Methods

Overriding a method means to overwrite the existing method. Often, it is _required_ to override a method if you are using an interface that is expecting specific methods to be available (these methods are usually stumps or something similar anyway). Other times, it makes sense to override a method; for example, almost every class uses the `equals()` method to check for equality (**not** `==`), and the classes that do not use the `hashCode()` method to check for equality (which should always be overrided if you override `equals()`).

To override a method, simply type `@Override` on the line above (or even directly in front of, at the beginning) of the line that lists the method.

**<font size="4">Popular Override - equals()</font>** 

The `equals()` method is popular to override, as almost all classes use `equals()` to check for equality amongst objects (and the ones that do not use `hashCode()`).  `equals()` must return a boolean true/false; usually all primitives in the two classes are compared to eath other, and if they all match, the objects are considered to be equal.

Here is an example of `equals()` for my `School` class I wrote:
```
    @Override
    public boolean equals(Object otherSchool) {
        if(otherSchool == this) { return true; }

        if (!(otherSchool instanceof School)) {
            return false;
        }
        School foreign = (School)otherSchool;
        return this.name==foreign.getName() && this.address==foreign.getAddress() && this.phone==foreign.getPhone();
    }
```
* `name`, `address`, and `phone` are the three primitives (Strings) in the `School` class, so they are all compared (`==` can be used here).
* The line `if(otherSchool == this) { return true; }` is comparing if the locations in memory are equal.

**<font size="4">Popular Override - hashCode()</font>** 

The `hashCode()` method is popular to override - and recommended if you override `equals()` - as some classes use `hashCode()` to check for equality amongst objects.  `hashCode()` must return an int representing a 'hash' of sorts; The 'hash' must utilize all primitives.

Here is an example of `hashCode()` for my `School` class I wrote:
```
    @Override
    public int hashCode() {
        return Objects.hash(this.name, this.address, this.phone);
    }
```
* Note that this is very simple: just list all primitives used by the class in the method `Objects.hash()`.
* This requires you to import `java.util.Objects`.

There is also the old method of doing this - I list it for historical purposes. This was the 'standard' way to override the hashCode before JDK 7 (use the other method now, though):
```
    @Override
    public int hashCode() {
        int result = 17;
        result = 31 * result + name.hashCode();
        result = 31 * result + age;
        result = 31 * result + passport.hashCode();
        return result;
    }
```
* This is not for any specific class I have written here, but know this class has three variables: two strings (name and passport) and an age (int).

**<font size="4">Popular Override - toString()</font>** 

`toString()` is used often enough to say that each class should override this method so it works with their given class; the example from my `School` class is:
```
    @Override public String toString() {
        return this.name + ", "+ this.address + ", " + this.phone;
    }
```

## Deep Copy Method

Many 'copy' functions are <font color="purple">shallow copies</font> - meaning, you are told there is a copy of the object made and stored to a new object, but in reality, it simply references the point in memory of where the object resides and saves that. This means that if the initial object is changed, the copy **will** change as well - and vice versa. Usually this is not desirable if you make a copy - usually its more desirable to make a <font color="purple">deep copy</font>, which will avoid the original - or copy - changing when the other is changed.

To ensure this happens, we usually need to make a separate copy of the object and then copy in all of its primitives, one by one; in addition, if the object has classes, we need to do this for each individual class inside the object as well (or deploy its own deep copy method, if it has one). An example is the `createCopy()` method in my [class example](learn_to_code/java/java_basics?id=class-example):
```
    public School createCopy() {
        School retVal = new School(this.name, this.address, this.phone);
        return retVal;
    }
```
* We make a `School` object to start, which may be a bit weird as we are in the `School` class itself, but it is what it is.
* We then have to set all of its primitives using our object's primitives. This is easy in this case as I do this in the constructor, but if this is not avaiable you will have to use the object's getters and setters to do this.
* Finally, we return the object we just created, finalizing the <font color="purple">deep copy</font>.


## General Notes on Classes

Notes:
* Class names start with upper-case letters.
* Static methods / functions in the same class do not need the class name to be called.
* The `this` keyword can be useful.
 * For static functions in the class, `this` just represents that function.
 * For nonstatic functions / methods, `this` will represent that _instance_ of the method /function in the object.
* There can be multiple classes in a single .java file, but this is usually not the case.
 * This stems from an old Java practice that allowed multiple classes so long as only one was public, but this is frowned upon by developers now.

## Class Example

This is an example of a `School` class:

```
import java.util.Objects;

public class School {
    String name;
    String address;
    String phone;


    //this is a constructor, which is used to initialize a School Object
    //Note there is NO type, not even void - and there is no scope either
    School(String name, String address, String phone) {
        // the 'this' references the class variable; the variable that does not use the 'this' comes from the parameters
        this.address = address;
        this.name = name;
        this.phone = phone;
    }

    //this is another constructor if the only info you know is the name
    School(String name) {
        // the 'this' references the class variable; the variable that does not use the 'this' comes from the parameters
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPhone() {
        return this.phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public String getAddress() {
        return this.address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    //I like to make a createCopy() method in order to make a deep copy of the object
    public School createCopy() {
        School retVal = new School(this.name, this.address, this.phone);
        return retVal;
    }

    //it should be noted that == only works for primitive variables; for everything else, == just checks to see if its the same location in memory
    //most code that checks to see if two objects are the same uses the equals() method, so its important its overwritten for most (or maybe every) class
    @Override
    public boolean equals(Object otherSchool) {
        if(otherSchool == this) { return true; }

        if (!(otherSchool instanceof School)) {
            return false;
        }
        School foreign = (School)otherSchool;
        return this.name==foreign.getName() && this.address==foreign.getAddress() && this.phone==foreign.getPhone();
    }


    //if we override equals, its good to override hashCode() as this is used to check for equality in hash-based collections such as HashMap, HashSet, and Hashtable
    @Override
    public int hashCode() {
        return Objects.hash(this.name, this.address, this.phone);
    }

    @Override public String toString() {
        return this.name + ", "+ this.address + ", " + this.phone;
    }
}
```

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

# Strings

Strings are actually classes / objects with functions, so much of how they are treated are just like objects; in most cases they can also be treated like primitives, although they are not primitive.

> Strings _can_ be `null`.

## Combining Strings

```
//Strings can be combined with the + operator
String str1 = "Brent";
String str2 = "Jackson";
System.out.println(str + " and " + str2 + " went to the park.");
```
* Note that it seems you cannot use the single quote to wrap a string (at least in Java 8).

## String Comparison

Code Example:
```
String str1 = "Brent";
String str2 = "Brent";
String str3 = "brent";
String str4 = "Jackson";
Object objString = str1;

//The compareTo() gives an integer as a return
////if its less than 0, the main string object is before / is 'greater than' the string passed to it
////if its 0 the strings are equal
////if its greater than 0, the main string object is after / is 'less than' the string passed to it
if (str1.compareTo(str4) > 0) {
	System.out.println("'" + str1 + "' comes after '" + str4 + "'");
} else if (str1.compareTo(str4) < 0) {
	System.out.println("'" + str1 + "' comes before '" + str4 + "'");
}

//compareToIgnoreCase is similar to compareTo, but obviously ignores the case
if (str3.compareToIgnoreCase(str2) == 0) { System.out.println("These strings are the same (compareToIgnoreCase())."); }

//the equals() method simply gives a boolean as a result
if (str2.equals(objString)) { System.out.println("These strings are the same (equals())."); }

//finally, you can also use the == operator
if (str2 == objString) { System.out.println("These strings are the same (== operator)."); }

```
* Note that using objString was a hit or miss - it did NOT work in compareToIgnoreCase().
* You may want to avoid using `==`, as this usually just compares memory locations. It _does_ work for primitives - as well as Strings - but you should avoid it otherwise if you can.

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

Foreach loops are built for [Arrays](learn_to_code/java/java_basics?id=arrays-and-lists):
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

# Arrays and Lists

## Basic Arrays

```
//simply declare an array
int[] myArrayOfNumbers; //preferred
int myArrayOfNumbers[]; //works, but not preferred

//actually create the array
myArrayOfNumbers = new int[10]

//you can do both in one step
int[] myArrayOfNumbers = new int[10];

//you can create the array immediately
double[] myDoubleArray = {1.22, 2.3456, 17.578, 99.2};

//if you want to get the length, you can just use
int myLength = myArrayOfNumbers.length;
```
* Arrays are treated like objects (as they sort of operate like one)

Note that [NULL](learn_to_code/java/java_basics?id=nulls) is represented as `null` and can be checked with basic operators; this can be used when iterating through arrays, specifically checking if the element has been set or not.

## ArrayLists

An <font color="green">ArrayList</font> can keep a list of objects that also acts like an array (sort of). ArrayLists are very common and are usually defined like:
```
List<Thread> myThreads=new ArrayList<Thread>();
```
* `List<>` represnets the _interface type_, and ArrayList<> represents the _implementation type_.
* The usage of `Thread` indicates the _type_ of object stored in the list; in our case it is a `Thread`, but this can be any non-primitive type (`String` and all other classes, but not simple things like `int`)
 * We listed `Thread` twice, but since we wrote `List<Thread>` we could have written `ArrayList<>()` as the first implies the second (unless they are different, i.e. the <font color="purple">interface type</font> and <font color="purple">implementation type</font> are different).

<font color="green">ArrayList</font>s are dynamic - you do not have to know their size beforehand. That said, if you wish to pre-define it, you can declare it like this:
```
List<Thread> myVar = new ArrayList<Thread>(10);
```
* This example sets the default to 10 objects in the <font color="green">ArrayList</font>, but if more are added it will simply add more and not error out.

!> If you would like to make an <font color="green">ArrayList</font> of a [primitive](learn_to_code/java/java_basics?id=primitive-variables), you _must_ use its class equivalent (so for example, do not use `int`, use `Integer`).

## Adding to ArrayLists

To add to an <font color="green">ArrayList</font> its:
```
myVar.add(myObject)
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* `myObject` is some object you are working with.

You could also add the item at a _specific_ position if you wanted; for example if you wanted to add to the first position of the index it would be:
```
myVar.add(0, myObject)
```

## Adding an ArrayLists to ArrayLists

To add to an entire <font color="green">ArrayList</font> to another <font color="green">ArrayList</font> its:
```
myVar.addAll(myArrayList)
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* `myArrayList` is some other <font color="green">ArrayList</font> you are working with that you wish to add to the <font color="green">ArrayList</font> `myVar`.
* If `myArrayList` is null this _will_ throw an error, so please account for that.

You could also add the <font color="green">ArrayList</font> at a _specific_ position if you wanted; for example if you wanted to add to the first position of the index it would be:
```
myVar.addAll(0, myArrayList)
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.

## Removing Objects from ArrayLists

> Practically speaking, you should [search](learn_to_code/java/java_basics?id=searching-through-arraylists) for the index of the object you wish to remove before you actually remove anything.

To remove an object from an <font color="green">ArrayList</font> by its index, use:
```
myVar.remove(3)
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* This removes the object at position 4 (which is referenced with a 3, as above).
* If the index you are trying to remove does not exist, this _will_ error out.

If you have a **range** of items you would like to remove you could do so with:
```
myVar.removeRange(startIndex, endIndex)
```
* `startIndex` and `endIndex` are `int`s that represent positions in the index.
* This will include the removal of `startIndex` but will _not_ remove `endIndex` - it will remove the position just before it.



## Determining Size of ArrayLists

To determine the size of an <font color="green">ArrayList</font>, use the `size()` method like so:
```
myVar.size()
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* This returns the size of the array in `int` form.
* Even though the array itself is zero based, `size()` is not - so if there is only one item in a list:
 * `size()` would return 1.
 * Using [get(0)](learn_to_code/java/java_basics?id=basic-arrays) on position 0 would return the element.
 * Using `get(1)` would result in an error.

## Getting Object At Index

This is where an <font color="green">ArrayList</font> really differs from an array - you cannot reference items a la `myVar[x]`, you must use a method to do so.

You can get an object at a specific index using `get()` like so:
```
myVar.get(2)
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* We list a `2`, meaning we want to get the object in position 3 (since the list is based on 0, 0 is considered the first item - so 2 would represent the 3rd, 9 would represent the 10th, etc).

!> If you use `get()` on an index that is out of range - say, the highest index is 10 and you tried to get position 11 - this _will_ error out. To avoid this, always [check the numberof elements in the list](learn_to_code/java/java_basics?id=determining-size-of-arraylists); in addition, know that the array is based on 0 but `size()` is _not_ zero-based - so if you use `size()` and it returns a 5, the highest number you can use in `get()` is 4.


## Searching Through ArrayLists

!> The [equals() method **must** be overwritten](learn_to_code/java/java_basics?id=overriding-methods) for searching to work!

You can search through an <font color="green">ArrayList</font> for an object like so:
```
myVar.contains(myObject)
```
* This returns a boolean - true if the object exists, false otherwise.
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* `myObject` is the object you are searching for.

Sometimes, you need the _specific_ location in the array of an object; to get the _first_ position it occupies, use `indexOf()`:
```

myVar.indexOf(myObject)
```
* This returns the index of the _first_ time the object is seen in the array (you can then use [get()](learn_to_code/java/java_basics?id=getting-object-at-index) to interact with the object, now that you have the index).
* If the object does not exist, a -1 is returned.

To get the _last_ position it occupies, use `lastIndexOf()`:
```

myVar.lastIndexOf(myObject)
```
* This returns the index of the _last_ time the object is seen in the array (you can then use [get()](learn_to_code/java/java_basics?id=getting-object-at-index) to interact with the object, now that you have the index).
* If the object does not exist, a -1 is returned.
* If this index equals the index found with `indexOf()`, there is only one instance of the object.

## Replacing Objects in ArrayLists

You can outright replace any object in an <font color="green">ArrayList</font> with the `set()` method:
```
myVar.set(intIndex, myObject)
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* `intIndex` is the position in the array that yo uwant to replace.
* `myObject` is the object you are going to insert into position `intIndex`.
* This sets the position `intIndex` to the object `myObject` - the object that currently resides at `intIndex` is completely overwritten.

## Converting an ArrayList to Array

Sometimes, you will have to convert your <font color="green">ArrayList</font> to an [Array](learn_to_code/java/java_basics?id=basic-arrays); to do so, its:
```
myVar.toArray()
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* This returns an array (not an <font color="green">ArrayList</font>) with a type that matches `myVar`.

You can also directly set everything to a specific type; for example, we can create an array, and then set the object type using that array.

For example:
```
      ArrayList<Integer> myVar = new ArrayList<Integer>();

      myVar.add(5);
      myVar.add(10);

      Integer myIntegers[] = new Integer[myVar.size()];
      myIntegers = myVar.toArray(myIntegers);
```
* This creates and populates ArrayList `myVar` and adds some items.
 * Note it uses an `Integer` class and _not_ the primitive `int` - ArrayLists _cannot_ use primitives.
* The last line saves the array to the `myIntegers`array.
 * `toArray(myIntegers)` means it automatically changes the type to that of `myIntegers`; usually you implicitly do this, but sometimes you need a similar class and not the exact class.

## Trimming ArrayLists

If you simply want to clear the unused space that the <font color="green">ArrayList</font> is occupying (for example, if it has shrunk considerably via removing items), use `trimToSize()`:
```
myVar.trimToSize()
```

To quickly dump / clear an <font color="green">ArrayList</font> its:
```
myVar.clear()
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.


## Cloning ArrayLists

To quickly clone an <font color="green">ArrayList</font> you will use the `clone()` method:
```
ArrayList<Thread> arrayListClone =  (ArrayList<Thread>) myVar.clone();
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* `arrayListClone` is the cloned list.

Note that simply using `clone()` copies a _reference_ to the initial object (called a _shallow_ copy); so if you change the initial object the new object will change, and vice versa.

To create a _deep_ copy - which can be modified without changing the original copy - you will have to use an `Interator` class; in addition, you will have to make either an external function or a method in your class that copies the primitives / everything else to another object. I show how t odo this in my [deep copy method](learn_to_code/java/java_basics?id=deep-copy-method).

Here is an example that uses my [School class](learn_to_code/java/java_basics?id=class-example):
```
ArrayList<School> schoolList = new ArrayList<>(); 
ArrayList<School> schoolListClone = new ArrayList<>();

Iterator<School> iterator = schoolList.iterator();

while(iterator.hasNext()) {
	//Add the object clones
    schoolListClone.add(iterator.next().createCopy());	
}
```
* `iterator.next()` is a representation of the current object from schoolList - and all of its methods are available to you (which is why `createCopy()` is available to us).
* Again, I want to stress that you have to create the `createCopy()` method yourself in the class; otherwise, if you do not have the ability to do that, you must get at the primitives in another way.

## Looping In Arrays and Lists

It is a necessity to loop through an Array or ArrayList - here are the different ways to do it.

It should be noted that each example here will utilize the [School class](learn_to_code/java/java_basics?id=class-example), with an initial setup as follows (using an ArrayList as an example):.

```
    //create an ArrayList of Schools
    List<School> mySchools=new ArrayList<>();

    //make a bunch of School objects we will eventually store to our listing
    School gaTech = new School("Georgia Institute of Technology");
    School WCU = new School("West Chester University of Pennsylvania");
    School albright = new School("Albright College");

    //add the School objects to our ArrayList
    mySchools.add(gaTech);
    mySchools.add(WCU);
	mySchools.add(albright);
```

**<font size="4">Traditional Looping</font>** 

Traditional looping uses a `for` loop, incrementing the index by one each time. Here is a classic example that simply prints the name of each school:
```
	for(int i = 0; i < mySchools.size();i++) {
		System.out.println(mySchools.get(i).getName());
	}
```
* This works for 
 * ArrayLists (as above).
 * Arrays (with some modifications).

**<font size="4">Item: Collection Method</font>** 

You can use the following:
```
    //use another method to cycle through all elements of the array
	for(School element: mySchools) {
    	System.out.println(element.getName());
	}
```
* This works for 
 * ArrayLists (as above).
 * Arrays.
* This cycles through each item in the list / array; each iteration stores the current object in `element` (or whatever you wish to name it), and the next iteration of the loop will use the next object in the array / list and store that to `element`.
 * `element` can be used just like the underlying object is used, but you _cannot_ make changes to it (well, you can, but it will not update the underlying object).

**<font size="4">Lambda Functions</font>** 

For ArrayLists, you can use a [lambda function](learn_to_code/java/java_basics?id=lambda-function) to loop through, which relies on the `forEach()` method:
```
	mySchools.forEach(
	
		//lambda expression
		(schoolElement) -> {
			System.out.println(schoolElement.getName());
		}
		
	);
```
* This works for ArrayLists (as above).
* This does <font color="red">not</font> work for Arrays.
* It is implied that whatever is within the `()` will represent each object in the ArrayList - in this case, each time the loop activates it will get the next object in the list and store it to `schoolElement`.
 * `schoolElement` can be used just like the underlying object is used, but you _cannot_ make changes to it (well, you can, but it will not update the underlying object).


**<font size="4">Using the Iterator Class</font>** 

You can also use the [Iterator class](learn_to_code/java/java_basics?id=iterator) to loop through every object in the ArrayList:
```
    Iterator<School> cloneIterator = mySchools.iterator();
	while(cloneIterator.hasNext()) {
    	System.out.println(cloneIterator.next().getName());
	}
```
* This works for ArrayLists (as above).
* This does <font color="red">not</font> work for Arrays.
* See the [ListIterator class](learn_to_code/java/java_basics?id=listiterator) for more information.

## ArrayList - Full Example

Here is an example that uses most of the methods available to <font color="green">ArrayList</font>s.  

This example uses my [School class](learn_to_code/java/java_basics?id=class-example):
```
import java.util.*;

public class Main {
    public static void main(String[] args) {

        //create an ArrayList of Schools
        List<School> mySchools=new ArrayList<>();

        //make a bunch of School objects we will eventually store to our listing
        School gaTech = new School("Georgia Institute of Technology");
        School hardKnocks = new School("Hard Knocks University");
        School WCU = new School("West Chester University of Pennsylvania");
        School secondWCU = new School("West Chester University of Pennsylvania");
        School laSalle = new School("LaSalle University");
        School albright = new School("Albright University");

        //add the School objects to our ArrayList
        mySchools.add(gaTech);
        mySchools.add(WCU);
        mySchools.add(laSalle);
        mySchools.add(albright);

        //do a deep clone of the school listing to print later
        ArrayList<School> schoolListClone = new ArrayList<>();

        Iterator<School> iterator = mySchools.iterator();
        while(iterator.hasNext()) {
            //Add the object clones
            schoolListClone.add(iterator.next().createCopy());
        }

        //use traditional method to cycle through all of our schools, printing them to the screen
        for(int i = 0; i < mySchools.size();i++) {
            System.out.println(mySchools.get(i).getName());
        }
        System.out.println("  "); //make an empty line on the screen

        if (mySchools.contains(secondWCU)) {
        //IF the ArrayList contains the secondWCU object - which is equivalent to WCU, which is present - continue

            //find the position of WCU (but we use secondWCU to demonstrate it doesnt have to be exactly the same object)
            int WCU_Index = mySchools.indexOf(secondWCU);

            System.out.println("The index of WCU in the ArrayList is " + WCU_Index + ", but we are going to replace with the school of hard knocks.");

            //replace WCU with the school of hard knocks
            mySchools.set(WCU_Index, hardKnocks);
        }

        //print out the new array using another cycle method, forEach and a lambda function
        mySchools.forEach(
                //lambda expression
                (schoolElement) -> {
                    System.out.println(schoolElement.getName());
                }
        );
        System.out.println(" ");

        //remove one item from the listing - specifically, albright
        if (mySchools.contains(albright)) {

            mySchools.remove(mySchools.indexOf(albright));
        }

        //convert the current ListArray to an Array
        School myArrayOfSchools[] = new School[mySchools.size()];
        myArrayOfSchools = mySchools.toArray(myArrayOfSchools);

        //use another method to cycle through all elements of the array
        for(School element: myArrayOfSchools) {
            System.out.println(element.getName());
        }
        System.out.println("  "); //make an empty line on the screen

        //finally, print the cloned copy to show it never changed using the final way to iterate through a list, using the Iterator class
        Iterator<School> cloneIterator = schoolListClone.iterator();
        while(cloneIterator.hasNext()) {
            System.out.println(cloneIterator.next().getName());
        }

    }
}
```


---

# ListIterator

!> I am going to be brief with the `ListIterator` class, as it is much easier for me to use [some of the other methods for looping](learn_to_code/java/java_basics?id=looping-in-arrays-and-lists). If you wish to see more in the ListIterator class, [you can check it out here](https://www.tutorialspoint.com/java/java_using_iterator.htm).


An <font color="green">ListIterator</font> object helps to cycle through lists of objects sequentially; the only requirement is that the list object must have a `listIterator()` method (as an example, `ArrayList` objects have both an `iterator()` _and_ a `listIterator()` method, `Array` objects do not). How `ListIterator`s work is it keeps a pointer variable that points to the 'current' object (or just before it, or just after the last object) and you traverse the list by moving this pointer.

The basic order of operations is:
1\. Create an `ListIterator` object and set it equal to the target object's `listIterator()` method (which returns a `ListIterator`); an example is `ListIterator<School> myIterator = mySchools.listIterator();`  
 * This will store things like the current pointer, beginning / end locations in memory, etc.  
 * The `ListIterator` object is the object you reference while moving through the objects - you will _not_ refer to the actual ArrayList again when cycling through the objects (outside of initializing the ListIterator).
 * I will refer to `myIterator` in these instructions.
2\. Using a combination of a [while loop](learn_to_code/java/java_basics?id=while-loops) and the iterator's `hasNext()` method, you will know if there are more items in the list.
3\. You use the `ListIterator`s `next()` method to get the next item in the list.

## Iterator vs ListIterator

There are actually two different classes of Iterators: `Iterator` and `ListIterator`. `Iterator` is much smaller - it only has the `hasNext()`, `next()`, and `remove()` methods.

As an example, here is how one would set a `ListIterator` object:
```
ListIterator<School> myIterator = mySchools.listIterator();  
```

And this is how the `Iterator` object would be set:
```
Iterator<School> myIterator = mySchools.iterator(); 
```

The major differences are the required methods (`listIterator()` vs `iterator()`) for the objects using an interator and the greatly reduced available methods for the `Iterator` class.

> Usually a simple `Iterator` is used over the more extensive `ListIterator`.

## Testing And Moving The Cursor

Initially, the cursor points to an area just before the first object (this is done so you must use `next()` to advance initially without weird oddities). You can use the `hasNext()` method (i.e. `myIterator.hasNext()`) to _check_ and see if there is another object after the cursor (note this does not not actually _move_ the cursor); conversely, `hasPrevious()` does the same thing, but backwards (again, it does not actually move the cursor though).

Usually, `hasNext()` or `hasPrevious()` is used in a while loop to _check_ if there are more objects to loop through - if there are, you actually _access_ that object with either `next()` or `previous()` (depending on if you are going backwards or forwards). 

An example (using my [School class](learn_to_code/java/java_basics?id=class-example)):
```
        //create an ArrayList of Schools
        List<School> mySchools=new ArrayList<>();

        //make a bunch of School objects we will eventually store to our listing
        School gaTech = new School("Georgia Institute of Technology");
        School WCU = new School("West Chester University of Pennsylvania");
        School albright = new School("Albright University");

        //add the School objects to our ArrayList
        mySchools.add(gaTech);
        mySchools.add(WCU);
        mySchools.add(albright);

        ListIterator<School> iterator = mySchools.listIterator();
		
		//check to see if there is an object that follows after the cursor's current location
        while(iterator.hasNext()) {
            //get the next object and call its 'getName()' method (which is a defined method of the School class).
            System.out.println(iterator.next().getName());
        }
```
* Notice how we do _not_ reference `mySchools` to utilize `getName()` - we use the `iterator` object.

## Manipulating the ArrayList with ListIterator

Its also possible to _directly_ manipulate the ArrayList through the `iterator` object:
* We can use the `add(myObject)` method of the operator to add `myObject` to the list immediately after the cursor (and, coincidentally, in front of the object that can be accessed with `next()`).
* We can use the `remove()` to remove the current object.
 * This _will_ error out if you have not used `next()` yet _or_ there are no objects in the list.
* We can use the `set(myObject)` to overwrite the current object last returned by a call to either `next()` or `previous()`.



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
* The pattern `yyyy/MM/dd HH:mm:ss` can be set in any combination by you,

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