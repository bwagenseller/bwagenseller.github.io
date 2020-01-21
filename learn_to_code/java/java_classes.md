# Java Classes

# Before We Begin

# Description of a Class

Java classes are a bit overwhelming but the truth is, they are fairly straightforward. A class can be considered a grouping of things; so for example, there could be a 'building' class/group, and members of this group are things like house, skyscraper, shed, outhouse, etc. The idea of a 'Building' is a bit vague - and at first, classes are a bit vague - but things that are _defined_ as a building - a house, for example - is a bit more granular.

Early in the java basics document I defined a [DragonBorn class](learn_to_code/java/java_basics?id=basic-file-setup-first-java-program); this was named from a concept in one of my favorite video games, [The Elder Scrolls V: Skyrim](https://en.wikipedia.org/wiki/The_Elder_Scrolls_V:_Skyrim). The [Dragonborn](http://elderscrolls.wikia.com/wiki/Dragonborn_%28Lore%29) refers to someone born with the blood and soul of a dragon, but the body of a mortal human; there are multiple people who were 'Dragonborn' throughout the history of Skyrim.

So, 'Dragonborn' is a class, but any _individual_ instance of a 'Dragonborn' is not a class, but a specific person (such as the main character in Skyrim). 

# General Notes on Classes

Notes:
* Class names start with upper-case letters.
* Static methods / functions in the same class do not need the class name to be called.
* There can be multiple classes in a single .java file, but this is usually not the case.
 * This stems from an old Java practice that allowed multiple classes so long as only one was public, but this is frowned upon by developers now.

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
* Note there is NO scope - it is assumed to be public.
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

# Extending Classes  

Its possible for a class to access another class' _public_ (but not private) methods as its own if it <font color="green">extends</font> that class. This section will cover classes extending other classes.  

!> A class can only extend **ONE** class, not multiple classes!  

## Superclass / Subclass 

A class can implement another class (or an [interface](learn_to_code/java/java_classes?id=interface)) - what this means is class `B` can extend class `A` - and when that happens, `B` can access / utilize all _public_ variables and methods (but not _private_ methods and variables) of `A` _in addition to_ any new variables or methods intorduced by `B`.  In this case, the base class (`A`) is the <font color="green">superclass</font> and the derivative `B` is the <font color="green">subclass</font>.  This is done via the `extends` keyword (in the [example](learn_to_code/java/java_classes?id=superclass-subclass-example), you can see that `Sedan` extends `Automobile`). 

!> A class can only extend **ONE** class, not multiple classes!  

## Superclass / Subclass Example

Here is an example of a <font color="green">superclass</font> `Automobile` and a <font color="green">subclass</font> `Sedan`, which will be explained in this section:    

```
public class Automobile {

    private String engineType;
    private String autoMake;
    private String autoModel;
    private int vin;

    public Automobile(int vin) {
        System.out.println("This is an Automobile! Its VIN is " + vin);
        this.vin = vin;
    }

    public String saySalesPitch() {
        return "Automobiles are the best!";
    }

    public void setEngineType(String engineType) {
        this.engineType = engineType;
    }

    public String getEngineType() {
        return this.engineType;
    }

    public void setMake(String autoMake) {
        this.autoMake = autoMake;
    }

    public String getMake() {
        return this.autoMake;
    }

    public void setModel(String autoModel) {
        this.autoModel = autoModel;
    }

    public String getModel() {
        return this.autoModel;
    }
}

public class Sedan extends Automobile {

    private int yearMade;
    int numSold;

    public Sedan() {
        //int vin
        super(123458);
        numSold = 0;
        System.out.println("This is a Sedan!");
    }

    @Override
    public void saySalesPitch() {
        System.out.println("Sedans are for families!");
    }

    public void increaseSoldCount() {
        numSold++;
    }

    public int getSoldCount() {
        return numSold;
    }

    public void setYearMade(int yearMade) {
        this.yearMade = yearMade;
    }

    public int getYearMade() {
        return this.yearMade;
    }

    public String returnBaseSalesPitch() {
        return super.saySalesPitch();
    }
}
```  

## Referencing The Superclass  

Its possible to reference the <font color="green">superclass</font> directly from the <font color="green">subclass</font>; usually this is not required (you can just name the public method or variable), but in some cases you will have to use the `super` keyword:  
* If you [overrode](learn_to_code/java/java_classes?id=overriding-methods) the <font color="green">superclass</font> method in the <font color="green">subclass</font> but you want to specifically use the <font color="green">superclass</font>' method.  
 * This is done in [the example](learn_to_code/java/java_classes?id=superclass-subclass-example) in `returnBaseSalesPitch()` via the line `return super.saySalesPitch();`.  
* If you need to explicitly call the constructor of the <font color="green">superclass</font> (see [here](learn_to_code/java/java_classes?id=constructors-and-supersubclasses)).  

> A <font color="green">subclass</font> can access all _public_ variables or functions of the <font color="green">superclass</font>, but it _cannot_ access private variables or functions of the <font color="green">superclass</font>.  

## Constructors and Super/Subclasses

Both the constructor of the <font color="green">superclass</font> and <font color="green">subclass</font> will run once the subclass is defined as an object, even if the object is defined as the <font color="green">subclass</font>, the <font color="green">superclass</font>' constructor will automatically run regardless of being explicitly called; that said, if the <font color="green">superclass</font> _requires_ a variable to be sent to its constructor, you _must_ include it in the <font color="green">subclass</font>' constructor and set it with the `super()` method.  

In [the example](learn_to_code/java/java_classes?id=superclass-subclass-example), the <font color="green">superclass</font> `Automobile` _requires_ an int (the <font color="purple">vin</font>) to be set in the constructor; we cannot set this directly in the constructor of `Sedan`, but we can _indirectly_ call the constructor of the <font color="green">superclass</font> via `super()`; for example, the constructor of `Sedan` has the line `super(123458);`, which is calling the constructor of `Automobile` _directly_ (and for training purposes, just sets the vin to a hardcoded value).  If this is _not_ done the code will error out.

> We only need to call `super()` in the <font color="green">subclass</font> _if_ the <font color="green">superclass</font> has required parameters in the constructor; if it does not, we do not have to call it (it will be called automatically).  

## Overriding Methods

Overriding a method means to overwrite the existing method. Often, it is _required_ to override a method if you are using an interface that is expecting specific methods to be available (these methods are usually stumps or something similar anyway). Other times, it makes sense to override a method; for example, almost every class uses the `equals()` method to check for equality (**not** `==`), and the classes that do not use the `hashCode()` method to check for equality (which should always be overrided if you override `equals()`).

To override a method, simply type `@Override` on the line above (or even directly in front of, at the beginning) of the line that lists the method.  

!> So it seems you do not _actually_ need to specify `@Override` in order to override a method (see discussion [here](https://stackoverflow.com/questions/4822954/do-we-really-need-override-and-so-on-when-code-java#4822969)); that said, it is suggested that you do use `@Override` as it A) Conveys to humans you should be overriding a method and B) Ensures you are _actually_ overriding a method (if you think you are and you use `@Override` and there is no such method in the [superclass](learn_to_code/java/java_classes?id=superclass-subclass), an error will be thrown).  

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

## Defining Subclass Objects

> Recall in the [example](learn_to_code/java/java_classes?id=superclass-subclass-example), you can see that `Sedan` extends `Automobile`) the <font color="green">superclass</font> is `Automobile` and the <font color="green">subclass</font> is `Sedan`.  


When it comes to defining a variable as a `Sedan` type we can simply do this:  
```
Sedan mySedan = new Sedan();
```

In reality there are two things going on here: you are defining something akin to [the interface](learn_to_code/java/java_classes?id=interface) (`Sedan mySedan`) and secondly you are creating a new object of particular type (`new Sedan()`). Defining the variable type will force `mySedan` to the rulesets and methods publicly available to `Sedan` but it will actually be of type `Sedan()`.  

This makes sense, but you can also do this:  
```
Automobile mySedan = new Sedan();
```
!> It must explicitly be '<font color="green">superclass</font> variableName = new <font color="green">subclass</font>();' - it _cannot_ be '<font color="green">subclass</font> variableName = new <font color="green">superclass</font>();'


Now [the interface](learn_to_code/java/java_classes?id=interface) follows that which is available to `Automobile`, although it is still technically an object of type `Sedan`. Defining the variable type will force `mySedan` to the rulesets and methods publicly available to `Automobile` but it will actually be of type `Sedan()`.  The implications of this are:  
* Any variables or methods added above and beyond `Automobile` will _not_ be available (so `mySedan.returnBaseSalesPitch()` will be invalid).  
* Any [overridden](learn_to_code/java/java_classes?id=overriding-methods) methods will use whatever is defined in the <font color="green">subclass</font> (in our case, `Sedan`).  

Why would we want to define the interface as the <font color="green">superclass</font> but use the <font color="green">subclass</font>? Truth be told this is usually not done, however there is one scenario where this is desirable: you anticipate that you may use a different <font color="green">subclass</font> in the future and you are not convinced you will need to use any additional methods or variables introduced in the <font color="green">subclass</font> (which will ensure your code does not break).  

## Downcasting / Upcasting

> <font color="green">Downcasting</font> and <font color="green">upcasting</font> are possible due to [polymorphism](https://en.wikipedia.org/wiki/Polymorphism_(computer_science).

We can convert a <font color="purple">superclass</font> to a <font color="purple">subclass</font> with <font color="green">downcasting</font> and a <font color="purple">subclass</font> to a <font color="purple">superclass</font> with <font color="green">upcasting</font>; in a sense, <font color="green">downcasting</font> and <font color="green">upcasting</font> effectively change the [interface](learn_to_code/java/java_classes?id=interface) of the object - that is to say, alter the available methods to the object - although its not exactly an [interface](learn_to_code/java/java_classes?id=interface) (an [interface](learn_to_code/java/java_classes?id=interface) is simply a set of instructions on how to build a class, whereas a <font color="purple">superclass</font> _explicitly_ defines them and what they do (and of course, they can be overridden).   

> Even though <font color="green">downcasting</font> and <font color="green">upcasting</font> can change the available methods to the object, using [Override](learn_to_code/java/java_classes?id=overriding-methods) in the <font color="purple">subclass</font> will mean that, no matter what, the version of the method as defined in the <font color="purple">subclass</font> will dominate.  

For example, in order to <font color="green">downcast</font> we could do this (using our [example](learn_to_code/java/java_classes?id=superclass-subclass-example)):  
```
        Automobile myCar = new Sedan();
        Sedan someCar = (Sedan) myCar;

        myCar.setModel("Ford");

        System.out.println(someCar.returnBaseSalesPitch() + " " + someCar.getModel());
```

This example makes an `Automobile` named `myCar`, then a _second_ variable named `someCar` which we can <font color="green">downcast</font> to the <font color="purple">subclass</font> `Sedan` -  and now `someCar` gains the methods available to `Sedan` (i.e. `returnBaseSalesPitch()` which is _not_ available to `Automobile`).  Curiously, `someCar` is a reference to `myCar`, so even though we set `myCar.setModel("Ford");` _after_ the downcast it will still display in `System.out.println(someCar.returnBaseSalesPitch() + " " + someCar.getModel());`.  

> The actual cast is done by putting the desired class in parenthesis before the object - so, for example, `(Sedan) myCar` casts `myCar` as a `Sedan` (note: it is not cast in-place, you have to set it equal to something or use it in a line otherwise).  

We could have also rolled it into one line with `((Sedan) myCar)`:  
```
        Automobile myCar = new Sedan();

        System.out.println(myCar.getClass().getName());

        myCar.setModel("Browna");

        System.out.println(((Sedan) myCar).returnBaseSalesPitch());
```  

> Note <font color="green">downcasting</font> only works of the _interface_; the _interface_ can be changed, but the object type cannot (so if the declaration ended in `new Automobile(12345)` this would not work).

Conversely, we can <font color="green">upcast</font> from a <font color="purple">subclass</font> to a <font color="purple">superclass</font>. Using our [example](learn_to_code/java/java_classes?id=superclass-subclass-example), we can <font color="green">upcast</font> from a `Sedan` to an `Automobile`:  
```
        Sedan myCar = new Sedan();

        System.out.println(myCar.getClass().getName());

        myCar.setModel("Browna");

        System.out.println(((Automobile) myCar).returnBaseSalesPitch());
```

We can also <font color="green">upcast</font> in a method call; for example, if you have an additional <font color="purple">subclass</font> `PickupTruck` that extends `Automobile` and you have a method that needs to accept objects of both types, you could use something like the following:
```
	private void someMethod(Automobile someVehicle) {
		...
	}
```

Using this, you can have a method `someMethod` that accepts either a `Sedan` _or_ a `PickupTruck` and either object type would be valid to be sent to this method; that said, you will be limited to only the methods available in `Automobile` for that object (although you can cheat a bit with overriding methods, as the overridden methods will use the <font color="green">subclass</font> version).  

---

# Interface 

A class can implement an <font color="green">interface</font>; an <font color="green">interface</font> is simply a set of instructions for which public-facing methods should be included in the class. Most of the same operations that are inherent to [superclasses](learn_to_code/java/java_classes?id=superclass-subclass) can be applied to <font color="green">interfaces</font>. For example: 
* <font color="green">Interface</font>s can be used as thar variable type in a method, and then that method will accept any class that implements (or inherits) that <font color="green">interface</font>.  
* <font color="green">Interface</font>s can be used in [downcasting and upcasting](learn_to_code/java/java_classes?id=downcasting-upcasting).  

In addition to this, an <font color="green">interface</font> lays out the functions that are _required_ to be present in the class that is implementing the <font color="green">interface</font>; this means that if a method is mentioned in the <font color="green">interface</font>, and classes implementing it _must_ implement those named methods (along with their parameter setup and return value type). 

## Interface Example


**<font size="4">Automobile.java</font>**
```
package com.wagenseller;

public interface Automobile {

    public void setEngineType(String engineType);

    public String saySalesPitch();

    public String getEngineType();

    public void setMake(String autoMake);

    public String getMake();

    public void setModel(String autoModel);

    public String getModel();
}
```

**<font size="4">Sedan.java</font>**
```
package com.wagenseller;

public class Sedan implements Automobile {

    private String engineType=null;
    private String autoMake=null;
    private String autoModel=null;
    private int yearMade;
    private int numSold;


    public Sedan() {
        //int vin
        numSold = 0;
        System.out.println("This is a Sedan!");
    }

    public String saySalesPitch() {
        return "Sedans are for families!";
    }

    public void setYearMade(int yearMade) {
        this.yearMade = yearMade;
    }

    public int getYearMade() {
        return this.yearMade;
    }

    public void setEngineType(String engineType) { this.engineType = engineType; }

    public String getEngineType() { return this.engineType; }

    public void setMake(String autoMake) {
        this.autoMake = autoMake;
    }

    public String getMake() {
        return this.autoMake;
    }

    public void setModel(String autoModel) {
        this.autoModel = autoModel;

    }

    public String getModel() { return this.autoModel; }
}

```

**<font size="4">PickupTruck.java</font>**
```
package com.wagenseller;

public class PickupTruck implements Automobile {
    private String engineType=null;
    private String autoMake=null;
    private String autoModel=null;
    private int yearMade;
    private int numSold;


    public PickupTruck() {
        //int vin
        numSold = 0;
        System.out.println("This is a Pickup!");
    }

    public String saySalesPitch() {
        return "Pickups are for men with gun racks!";
    }

    public void setYearMade(int yearMade) {
        this.yearMade = yearMade;
    }

    public int getYearMade() {
        return this.yearMade;
    }

    public void setEngineType(String engineType) { this.engineType = engineType; }

    public String getEngineType() { return this.engineType; }

    public void setMake(String autoMake) {
        this.autoMake = autoMake;
    }

    public String getMake() {
        return this.autoMake;
    }

    public void setModel(String autoModel) {
        this.autoModel = autoModel;

    }

    public String getModel() { return this.autoModel; }
}
```

**<font size="4">Main.java</font>**
```
package com.wagenseller;

public class Main {

    private static String getSalesPitch(Automobile someVechicle) {
        return someVechicle.saySalesPitch();
    }



    public static void main(String[] args) {

        Automobile myCar = new Sedan();
        Automobile myTruck = new PickupTruck();

        System.out.println(getSalesPitch((Automobile) myCar) + " == Sales Pitch");
        System.out.println(getSalesPitch((Automobile) myTruck) + " == Sales Pitch");
	}
}
```

> I used [upcasting](learn_to_code/java/java_classes?id=downcasting-upcasting) in the `System.out.println()` lines as I wanted to show it could be done, but in this case it was unnecessary (`getSalesPitch(Automobile someVechicle)` would have done the upcasting by default).  

The <font color="green">interface</font> `Automobile` simply defines what should be _expected_ from a class that implements the interface; `Sedan` and `PickupTruck` implement all of the methods that are required. The `Main` program defines the Sedan like so:
```
Automobile myCar = new Sedan();
```

In reality there are two things going on here: you are defining the <font color="green">interface</font> (`Automobile myCar`) and then you are creating a new object of a particular variable type (`new Sedan()`). In the `Main` code in the example above, we take advantage of [upcasting](learn_to_code/java/java_classes?id=downcasting-upcasting) so the method `getSalesPitch(Automobile someVechicle)` will accept eithe a `Sedan` _or_ a `PickupTruck`.  

## Extending Interfaces

Its possible to add methods to an existing <font color="green">interface</font> - simply use the word `extends` after the <font color="green">interface</font> name where defined in the <font color="green">interface</font>'s file.  

For example, we could have another <font color="green">interface</font> `Truck` extend `Automobile` like so:
```
public interface Truck extends Automobile {
    public int DieselGasMilage();
}
```  

Any class that would implement `Truck` would have to implement `DieselGasMilage()` _in addition to_ the methods in `Automobile`.  

## Multiple Interfaces

Its also possible for a class to extend _multiple_ <font color="green">interface</font>s - for example, say we had this very simple `Truck` <font color="green">interface</font>:  
```
public interface Truck extends Automobile {
    public int DieselGasMilage();
}
```  

We could have `PickupTruck` implement _both_ `Automobile` and `Truck` like so:  
```
public class PickupTruck implements Automobile, Truck {
...
}
```  

Any class that would implement `Truck` would now have to implement `DieselGasMilage()` (the only method in `Truck`) _in addition to_ the methods in `Automobile`.  

---  

# Abstract Classes  



An <font color="green">abstract class</font> is a mix of [extending a class](learn_to_code/java/java_classes?id=extending-classes) and [implementing an interface](learn_to_code/java/java_classes?id=interface).  Like an [interface](learn_to_code/java/java_classes?id=interface) you cannot directly instantiate an <font color="green">abstract class</font>, but other classes _can_ extend your <font color="green">abstract class</font> (as well as implement all interfaces you wish).  The advantage of an <font color="green">abstract class</font> is the <font color="green">abstract class</font> can implement methods that will likely not change while abstracting methods (i.e. making the class that extends the <font color="green">abstract class</font> implement the methods).   

> If an <font color="green">abstract class</font> actually defines a method, any class that extends it will not have to re-write that class if it does not wish to do so.  The methods that are abstract, however, _must_ be written by the class that extends the <font color="green">abstract class</font>.  

## Abstract Class Example  

**<font size="4">Automobile.java</font>**  
```
package com.wagenseller;

abstract public class Automobile {

    private String autoMake = "";
    public String autoModel = "";
    int employeeDiscount;

    Automobile(int employeeDiscount) {
        this.employeeDiscount = employeeDiscount;

        System.out.println("This is an Automobile constructor!");
    }

    abstract void setEmployeeDiscount(int employeeDiscount);

    public int getEmployeeDiscount() {
        return this.employeeDiscount;
    }

    public void setMake(String autoMake) {
        this.autoMake = autoMake;
    }

    public String getMake() { return this.autoMake; }

    public void setModel(String autoModel) {
        this.autoModel = autoModel;
    }

    public String getModel() {
        return this.autoModel;
    }

    public String saySalesPitch() {
        return "Please buy our automobile!";
    }
```  

**<font size="4">Truck.java</font>**  
```
package com.wagenseller;

abstract class Truck extends Automobile  {

    Truck(int employeeDiscount) {
        //we are forced to call the constructor in Automobile
        super(employeeDiscount);

        System.out.println("This is a basic Truck constructor!");
    }

    abstract String setDieselGasMilage();
}
```  

**<font size="4">PickupTruck.java</font>**  
```
package com.wagenseller;

public class PickupTruck extends Truck {
    private int bedLength = 0;

    public PickupTruck(int employeeDiscount) {
        //we are forced to call the constructor in Truck (which, in turn, will call the constructor in Automobile)
        super(employeeDiscount);

        System.out.println("This is a Pickup Truck constructor!");
    }

    public String saySalesPitch() {
        return "Pickups are for men with gun racks!";
    }

    public void setEmployeeDiscount(int employeeDiscount) {
        this.employeeDiscount = employeeDiscount;
    }

    public String setDieselGasMilage() {
        return "50 mpg";
    }

    private void printEmployeeDiscount() {
        System.out.println(this.getEmployeeDiscount());
    }

    private void setBedLength(int bedLength) { this.bedLength = bedLength; }

    private int getBedLength() { return this.bedLength; }
}
```  

**<font size="4">Main.java</font>**  
```
package com.wagenseller;

public class Main {

    public static void main(String[] args) {

		Automobile myCar = new PickupTruck(50);

        myCar.setMake("Ford");
        System.out.println("My Pickup is a " + myCar.getMake() + "!");
    }
}
```  

In our example, `PickupTruck` extends the abstract class `Truck`, which, in turn, extends the abstract class `Automobile`.  

## Declaring Abstract Classes / Methods  

As said in the opener for thie section, <font color="green">abstract class</font>es are similar to [implementing an interface](learn_to_code/java/java_classes?id=interface) - an <font color="green">abstract class</font> cannot be directly instantiated (like an interface); furthermore, much like an interface, you can declare methods in the <font color="green">abstract class</font> that _must_ be implemented by any class that extends it.

To declare an <font color="green">abstract class</font> we can look to [our abstract class example](learn_to_code/java/java_classes?id=abstract-class-example):  
```
abstract public class Automobile {
```  
* The <font color="green">abstract class</font> _cannot_ be `private` or `protected`.  

To declare an abstract method in the <font color="green">abstract class</font>, we can look to [our abstract class example](learn_to_code/java/java_classes?id=abstract-class-example) for an example:  
```
abstract void setEmployeeDiscount(int employeeDiscount);
```  
* This class is not fleshed out here, only defined; any class that will extend the <font color="green">abstract class</font> _must_ flesh this method out, though.  
* We could have set this to `public` or `protected`; `private` is _not_ allowed as a modifier on an abstract class.  

## Similarities w/ Interfaces + Extensions  

> Classes that extend an <font color="green">abstract class</font> are free to implement any number of [interfaces](learn_to_code/java/java_classes?id=interface); that said, extending an <font color="green">abstract class</font> still counts as extending a class, and a class is allowed to extend only _one_ class!  

An <font color="green">abstract class</font> is similar to [interfaces](learn_to_code/java/java_classes?id=interface) in that: 
* They cannot be instantiated directly.  
* They lay out the basic blueprints for whatever implements it.

In addition, extending an <font color="green">abstract class</font> is similar to [extending a class](learn_to_code/java/java_classes?id=extending-classes) in almost every aspect, with some mentionable similarities:
* You cannot access the <font color="green">abstract class</font>' private variables or methods.  
* You are required to satisfy all requirements of the <font color="green">abstract class</font>' constructor in the constructor of the non-abstract class.  

The one noted difference is abstract methods declared in the <font color="green">abstract class</font> _must_ be declared AND must be `public` _or_ `protected`.

## Abstract Class Extensions and Implementations

Its certainly possible for an <font color="green">abstract class</font> `B` to extend another <font color="green">abstract class</font> `A`; furthermore, `B` is _not_ required to implement any of the `abstract` methods defined in `A`; however, as soon as a non-abstract class extends `B`, that class _must_ implement any `abstract` methods not implemented in `B` **or** `A`.  

Conversely, an <font color="green">abstract class</font> that implements an [interface](learn_to_code/java/java_classes?id=interface) does _not_ have to implement any method of that interface; however, again, any non-abstract class that extends the <font color="green">abstract class</font> is _required_ to implement any method in the interface not implemented in the <font color="green">abstract class</font>.  

## Use Abstract Class or Interface?  

> This info appears on [Oracle's Java documentation page](https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html).  

Sometimes you wonder if you should use an [interface](learn_to_code/java/java_classes?id=interface) or if you should use an <font color="green">abstract class</font>.  Use an <font color="green">abstract class</font> if:  
* You want to write some of the base code without having to re-write it for closely related classes.  
* Classes that extend your <font color="green">abstract class</font> will have common methods or fields.  
* You want to have non-static or non-final fields.  

Conversely, you should use an [interface](learn_to_code/java/java_classes?id=interface) if:  
* Unrelated classes would use it.  
* You want to take advantage of [casting](learn_to_code/java/java_classes?id=downcasting-upcasting) the interface type.  

---  

# Identifying Class From Object 

Its fully possible to get the variable type from the variable itself, as well as see if its an instance of another class (or [interface](learn_to_code/java/java_classes?id=interface)). Matter of fact, you can get a host of information from the object's `getClass()` method (every object has access to `getClass()`). 

## Identifying Class Example

> This example re-uses the classes **Automobile.java**, **Sedan.java**, and **PickupTruck.java** from the [interface example](learn_to_code/java/java_classes?id=interface-example) - so we will not replicate them here, please go there and copy them.  

**<font size="4">Main.java</font>**  
```
package com.wagenseller;

import java.util.Arrays;

public class Main {

    public static void acceptAnObject(Object someObject, String description) {

		//check to see if the object is an instance of Automobile
        if (someObject instanceof Automobile) {
            System.out.println(description + " - registers as an Automobile with instanceof!");
        }

        if  (someObject instanceof Sedan) {
            System.out.println(description + " - registers as a Sedan with instanceof!");
        }

        if  (someObject instanceof PickupTruck) {
            System.out.println(description + " - registers as a Pickup Truck with instanceof!");
        }

		//check to see if the object is an instance of Automobile (using a different method)
        if (someObject.getClass() == Automobile.class) {
            System.out.println(description + " - registers as an Automobile.class with '.getClass() == .class'!");
        }

        if (someObject.getClass() == Sedan.class) {
            System.out.println(description + " - registers as a Sedan.class with '.getClass() == .class'!");
        }

        if (someObject.getClass() == PickupTruck.class) {
            System.out.println(description + " - registers as a PickupTruck.class with '.getClass() == .class'!");
        }

        if (someObject instanceof Automobile) {
		
			//this is required to get a listing of all associated interfaces of the object
            Class[] i = someObject.getClass().getInterfaces();
			
            System.out.println(description + " - downcasting to Automobile! Currently of type '" + someObject.getClass().getTypeName() + "' and current class name = '" + someObject.getClass().getName() + "' with interfaces of " + Arrays.asList(i) );

			// downcast from Object to Automobile
            Automobile castObject = (Automobile) someObject;
        }
    }

    public static void main(String[] args) {

        Sedan mySedan = new Sedan();
        PickupTruck myPickupTruck = new PickupTruck();

        acceptAnObject(mySedan, "Object is a Sedan");
        System.out.println("-------------------------------------------------------");
        acceptAnObject(myPickupTruck, "Object is a Pickup Truck");
    }
}
```

This is the output:  
```
Object is a Sedan - registers as an Automobile with instanceof!
Object is a Sedan - registers as a Sedan with instanceof!
Object is a Sedan - registers as a Sedan.class with '.getClass() == .class'!
Object is a Sedan - upcasting to Automobile! Currently of type 'com.wagenseller.Sedan' and current class name = 'com.wagenseller.Sedan' with interfaces of [interface com.wagenseller.Automobile]
-------------------------------------------------------
Object is a Pickup Truck - registers as an Automobile with instanceof!
Object is a Pickup Truck - registers as a Pickup Truck with instanceof!
Object is a Pickup Truck - registers as a PickupTruck.class with '.getClass() == .class'!
Object is a Pickup Truck - upcasting to Automobile! Currently of type 'com.wagenseller.PickupTruck' and current class name = 'com.wagenseller.PickupTruck' with interfaces of [interface com.wagenseller.Automobile]
```

## .getClass() == .class Comparison

> The examples used here use the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example).  

We can check the object's class by comparing it to the class' `.class` property; in the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example), this is done in the method `acceptAnObject(Object someObject, String description)`:  
```
if (someObject.getClass() == Automobile.class)
...
if (someObject.getClass() == Sedan.class)
...
if (someObject.getClass() == PickupTruck.class)
...
```  

This works - if `Object someObject` is one of those classes (whose structure is literally `ClassName.class`), the `if` will be triggered. The important distinction with this is the class must match _exactly_ - for `Sedan` for example, this only triggered for the comparison to `Sedan.class` and _not_ the `Automobile.class`line.  

> For more on the example that compares `.getClass() == .class` and `instanceof`, see [this stackoverflow article](https://stackoverflow.com/questions/4989818/instanceof-vs-getclass).  

## instanceof Comparison

> The examples used here use the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example).  

!> If you need to match the class _exactly_, you may be better off using the [.getClass() == .class](learn_to_code/java/java_classes?id=getclass-class-comparison) comparison method.  

`instanceof` checks to see if an object's class is an instance of another class, _including_ superclasses _and_ [interfaces](learn_to_code/java/java_classes?id=interface). For example, the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example) checks to see if an object (in our example, `Object someOBject`) is an `Automobile` class (which is actually an [interface](learn_to_code/java/java_classes?id=interface), a `Sedan` class, or a `PickupTruck` class. Edited for brevity: 
```
if (someObject instanceof Automobile)
...
if  (someObject instanceof Sedan)
...
if  (someObject instanceof PickupTruck)
```  

For the object defined in main as `PickupTruck myPickupTruck = new PickupTruck();`, this triggered the following `instanceof` lines:   
```
Object is a Pickup Truck - registers as an Automobile with instanceof!
Object is a Pickup Truck - registers as a Pickup Truck with instanceof!
```

So clearly, the `if` statements comparing the `instanceof` to `Automobile` _and_ `PickupTruck` were triggered. `instanceof` is good if you need to cast a wide (or general) net when looking to see if an object shares an ancestor with the `.class` you provide. Please note that I also created another class called `ExtendedPickup` (which extended `PickupTruck`) and `ExtendedPickup` triggered `Automobile`, `PickupTruck`, _and_ `ExtendedPickup` (I did not inclue `ExtendedPickup` in the example, but this was the behavior).  

> For more on the example that compares `.getClass() == .class` and `instanceof`, see [this stackoverflow article](https://stackoverflow.com/questions/4989818/instanceof-vs-getclass).  

## Generalizing a Java Variable

> The examples used here use the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example).  

Outside of [primatives](learn_to_code/java/java_basics?id=primitive-variables), all Java classes are a [subclasses](learn_to_code/java/java_classes?id=superclass-subclass) of the `Object` class. This is helpful, because if we need to accept an object as a variable in a method and that object may be of a variety of types, we can simply declare the interface as `Object` for the variable and then we can check (and then cast) that variable using [downcasting or upcasting](learn_to_code/java/java_classes?id=downcasting-upcasting).  

In the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example), the generalized object `Object someObject` is passed as a referenced variable to `acceptAnObject`, and in the code we check to see the class of is checked using either the [.getClass() == .class](learn_to_code/java/java_classes?id=getclass-class-comparison) method or the [instanceof](learn_to_code/java/java_classes?id=instanceof-comparison) method.  For our purposes, we will look at an [example](learn_to_code/java/java_classes?id=identifying-class-example) that uses `instanceof`:  
```
        if (someObject instanceof Automobile) {
            //this is required to get a listing of all associated interfaces of the object
            Class[] i = someObject.getClass().getInterfaces();

            System.out.println(description + " - downcasting to Automobile! Currently of type '" + someObject.getClass().getTypeName() + "' and current class name = '" + someObject.getClass().getName() + "' with interfaces of " + Arrays.asList(i) );

			// downcast from Object to Automobile
            Automobile castObject = (Automobile) someObject;
        }
```

If `someObject` was an instance of the [interface](learn_to_code/java/java_classes?id=interface) `Automobile`, we printed some class specific information about the object, but we also [downcasted](learn_to_code/java/java_classes?id=downcasting-upcasting) it too (from `Object` to `Automobile`, remember all Java classes use `Object` as a base for the parent) in the line:  
```
Automobile castObject = (Automobile) someObject;
```

This was an important step, because before this point, `someObject` - while actually of type `Sedan` or `PickupTruck` - was still using the `Object` interface, which usually isnt very helpful.  The above allows is to use [downcasting](learn_to_code/java/java_classes?id=downcasting-upcasting) to convert that interface from an `Object` to an `Automobiile` so we can then use the methods available to the `Automobile` interface.  Moving forward, we can treat `castObject` as we would any `Automobile` object, which is very helpful.

> The fact that all objects are of base `Object` and can be downcasted to be a more approriate interface is a very powerful feature in Java.  

---  

# Getting an Object's Class

> The official object we will use is `String someString`, but it could literally be any object variable that is not a [primative](learn_to_code/java/java_basics?id=primitive-variables).  

The ability to get any object's class is an important feature of Java objects; all java objects have a `getClass()` method, and more information can be extracted from there; for our examples, this will be `someString.getClass()`.  

!> I am only going to mention a few class properties that I find useful, but know there are dozens of properties to peruse.  

Class[] i = someObject.getClass().getInterfaces();
System.out.println(description + " - upcasting to Automobile! Currently of type '" + someObject.getClass().getTypeName() + "' and with interfaces of " + Arrays.asList(i) );
Object is a Pickup Truck - upcasting to Automobile! Currently of type 'com.wagenseller.PickupTruck' and current class name = 'com.wagenseller.PickupTruck' with interfaces of 


## Getting Class Name

To get a class name, use `.getClass().getName()` like so:
```
someString.getClass().getName()
```

This will return the `packagename.className` (in the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example), this was `com.wagenseller.PickupTruck` for the `PickupTruck` class).  

## Getting Class TypeName

To get a class type name, use `.getClass().getTypeName()` like so:
```
someString.getClass().getTypeName()
```

This is "an informative string for the name of this type", but is not the official name - for that, use [the class name](learn_to_code/java/java_classes?id=getting-class-name).  

This will return the `packagename.className` (in the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example), this was `com.wagenseller.PickupTruck` for the `PickupTruck` class).  

## Getting Class Interfaces 

Its possible to get class interfaces (with an 's', as there can be multiple interfaces). For example, we can get the interfaces in an array of `Class` objects like so:
```
Class[] myInterfaces = someString.getClass().getInterfaces();
System.out.println("Interfaces: " + Arrays.asList(myInterfaces) );
```

In the [identifying class example](learn_to_code/java/java_classes?id=identifying-class-example), this returned the list of `[interface com.wagenseller.Automobile]` for the `PickupTruck` class. 

---  

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

# Singleton Classes

A <font color="green">singleton class</font> is a class that has only _one_ instance across the entire JVM - this is helpful if you need a class to be instantiated only once and be a central object that every part of the code can access (in other words, you cannot create multiple, independent objects with a singleton class, you can only create one, and all others are simply references to the initial one). This may be good for resources (database connections, sockets, etc).

You may wonder how a <font color="green">singleton</font> differs from a static class:  
* Singleton classes do not break the concepts of object-oriented programming, whereas static classes do.  
* You are actually creating an instance of the <font color="green">singleton</font> (albeit in an abstract fashion), whereas the static class is _not_ instanced.  
* <font color="green">Singleton</font> objects are stored in the Heap memory, whereas static classes are stored on the stack (which is why <font color="green">singleton</font>s are available throughout the JVM).  

You do not explicitly declare a <font color="green">singleton</font>, but there are a few things you must do to ensure your class is indeed a <font color="green">singleton</font>:  
* Declare a `private static` variable of the class itself inside the class (a bit recursive, but it is what it is).  
* Use a static <font color="purple">factory</font> that grants access to the object / returns the instance of the `private static` instance of itself.  
* Use a private constructor (which is usually not private) in order to disallow outside entities from instantiating the <font color="green">singleton</font>.  

## Singleton Example

**<font size="4">DatabaseSingleton.java</font>**  
```
package com.wagenseller;

public class DatabaseSingleton {

    // You need to have a private static variable of the same type as the class
    private static DatabaseSingleton databaseSingletonInstance = null;

    private String databaseIP;

    // The constructor MUST be private
    private DatabaseSingleton() {
            this.databaseIP = "127.0.0.1";
    }

    // There must be a public static method that returns the private static variable above;
    // also, this must set it (using the private constructor, which it is allowed to do) if its null
    public static DatabaseSingleton getInstance() {
        if (databaseSingletonInstance == null) { databaseSingletonInstance = new DatabaseSingleton(); }
        return databaseSingletonInstance;
    }

    public void setIP(String databaseIP) { this.databaseIP = databaseIP; }

    public String getIP() { return this.databaseIP; }
}
```  

**<font size="4">Main.java</font>**  
```
package com.wagenseller;

public class Main {

    public static void main(String[] args) {

		// Since we do not instantiate singletons, simply run getInstance() 
        DatabaseSingleton dd_singleton = DatabaseSingleton.getInstance();
        DatabaseSingleton ANOTHER_dd_singleton = DatabaseSingleton.getInstance();

        System.out.println("dd_singleton IP: " + dd_singleton.getIP());
        System.out.println("ANOTHER_dd_singleton IP: " + ANOTHER_dd_singleton.getIP());

        //set ip of only dd_singleton
        System.out.println("Setting ONLY dd_singleton's IP...");
        dd_singleton.setIP("192.168.1.1");

        // BOTH IPs will be 192.168.1.1 now, even though we only set it for dd_singleton
        System.out.println("dd_singleton IP: " + dd_singleton.getIP());
        System.out.println("ANOTHER_dd_singleton IP: " + ANOTHER_dd_singleton.getIP());
    }
}
```  

This prints:  
```
dd_singleton IP: 127.0.0.1
ANOTHER_dd_singleton IP: 127.0.0.1
Setting ONLY dd_singleton's IP...
dd_singleton IP: 192.168.1.1
ANOTHER_dd_singleton IP: 192.168.1.1
```  


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

