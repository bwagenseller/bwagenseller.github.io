# Java Classes

# Before We Begin

# Description of a Class

Java classes are a bit overwhelming but the truth is, they are fairly straightforward. A class can be considered a grouping of things; so for example, there could be a 'building' class/group, and members of this group are things like house, skyscraper, shed, outhouse, etc. The idea of a 'Building' is a bit vague - and at first, classes are a bit vague - but things that are _defined_ as a building - a house, for example - is a bit more granular.

Early in the java basics document I defined a [DragonBorn class](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program); this was named from a concept in one of my favorite video games, [The Elder Scrolls V: Skyrim](https://en.wikipedia.org/wiki/The_Elder_Scrolls_V:_Skyrim). The [Dragonborn](http://elderscrolls.wikia.com/wiki/Dragonborn_%28Lore%29) refers to someone born with the blood and soul of a dragon, but the body of a mortal human; there are multiple people who were 'Dragonborn' throughout the history of Skyrim.

So, 'Dragonborn' is a class, but any _individual_ instance of a 'Dragonborn' is not a class, but a specific person (such as the main character in Skyrim). 

# Class vs Object

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

# Defining Scopes in Classes

Scope, in computer science terms, can be boiled down to 'what parts of the code have visibility to this entity'; practically, we must worry about the scope of several things, usually functions or variables. For example, some variables can only be seen inside of specific classes (or functions) and cannot be referenced outside of that class or function; other times, variables are _global_ and can be referenced anywhere in the code.

When class functions are defined as _public_, those functions can be referenced from entities outside of the class; when declared _private_, only other functions inside the class itself can access private functions. Using our [DragonBorn class](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program) class, the DragonBorn class function `public static void witnessDragonBorn()` is private; if there exists some other Java code that uses the DragonBorn class, it could create a DragonBorn object and call this function directly:
```
...
DragonBorn heroOfSkyrim = new DragonBorn();
heroOfSkyrim.witnessDragonBorn();
...
```

This would print "You.....you took it's soul."

# 'This' Keyword

You will frequently see the word `this` used in classes - it is meant to represent itself. It has two main uses:  
* For static functions in the class, `this` just represents that function.  
* For nonstatic functions / methods, `this` will represent that _instance_ of the method /function in the object.  

Often it is used for class variables; for example, if you have a variable `private String myString;` defined in the class and you wish to write a method that sets `myString` you can write something like this:  
```
public void setMyString(String myString) {
	this.myString = myString;
}
```  

The above may be confusing, but the `this.myString` represents the variable `private String myString;` that was defined in the class, and the plain `myString` represents the variable passed in `public void setMyString(String myString)`.  

# Defining Static Methods in Classes

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

# Defining Static Variables in Classes

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

# Class Constructors

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

# Overriding Methods

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

# Deep Copy Method

Many 'copy' functions are <font color="purple">shallow copies</font> - meaning, you are told there is a copy of the object made and stored to a new object, but in reality, it simply references the point in memory of where the object resides and saves that. This means that if the initial object is changed, the copy **will** change as well - and vice versa. Usually this is not desirable if you make a copy - usually its more desirable to make a <font color="purple">deep copy</font>, which will avoid the original - or copy - changing when the other is changed.

To ensure this happens, we usually need to make a separate copy of the object and then copy in all of its primitives, one by one; in addition, if the object has classes, we need to do this for each individual class inside the object as well (or deploy its own deep copy method, if it has one). An example is the `createCopy()` method in my [class example](learn_to_code/java/java_classes?id=class-example):
```
    public School createCopy() {
        School retVal = new School(this.name, this.address, this.phone);
        return retVal;
    }
```
* We make a `School` object to start, which may be a bit weird as we are in the `School` class itself, but it is what it is.
* We then have to set all of its primitives using our object's primitives. This is easy in this case as I do this in the constructor, but if this is not avaiable you will have to use the object's getters and setters to do this.
* Finally, we return the object we just created, finalizing the <font color="purple">deep copy</font>.


# General Notes on Classes

Notes:
* Class names start with upper-case letters.
* Static methods / functions in the same class do not need the class name to be called.
* There can be multiple classes in a single .java file, but this is usually not the case.
 * This stems from an old Java practice that allowed multiple classes so long as only one was public, but this is frowned upon by developers now.

# Class Example

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

