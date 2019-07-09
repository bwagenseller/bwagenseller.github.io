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

	public static void witnessDragonBorn() {
		System.out.println("You.....you took it's soul.");
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

## Defining Static Entities in Classes

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

## Class Constructors

Each time we wish to use a class (by creating an object), we must do something like so:
```
Sort sorter = new Sort();
```

This says 'we are creating a new object called 'sorter', it will be of type 'Sort', and we wish to create this now (with `new Sort()`)'. Each time this is done, the class **constructor** is called, which is a function that is called when an object is created. Technically, a constructor does not return a value because it returns the object itself.

The contstructor may do nothing at all, but oftentimes it helps initialize the object. There can be multiple constructors for the same class, each with a different parameter list; or, there may be no constructor at all, at which point Java will make one for the class (a bare bones constructor).



## General Notes on Classes

Notes:
* Class names start with upper-case letters.
* Static methods / functions in the same class do not need the class name to be called.
* The `this` keyword can be useful.
 * For static functions in the class, `this` just represents that function.
 * For nonstatic functions / methods, `this` will represent that _instance_ of the method /function in the object.
* There can be multiple classes in a single .java file, but this is usually not the case.
 * This stems from an old Java practice that allowed multiple classes so long as only one was public, but this is frowned upon by developers now.

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

The primitive variables are: byte, short, int, long, float, double, boolean, and char.

Sometimes, though, primitive types are required to be a class; therefore a primitive can be converted to  its matching class type using a concept called **auto-boxing**. The corresponding classes are Byte, Short, Integer, Long, Float, Double, Boolean, and Character.

# Strings

Strings are actually classes / objects with functions, so much of how they are treated are just like objects; in most cases they can also be treated like primitives, although they are not primitive.

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

# Arrays
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

Foreach loops are built for [Arrays](learn_to_code/java/java_basics?id=arrays):
```
double[] myDoubleArray = {1.22, 2.3456, 17.578, 99.2};
for (double element: myDoubleArray) {
	System.out.println(element);
}

```



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