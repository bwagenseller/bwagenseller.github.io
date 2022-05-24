# Java Data Structures

# Before We Begin

Arrays, lists, hashMaps, and other data structures are collections of [objects](learn_to_code/java/java_classes?id=class-vs-object). These are very useful when you need to store a collection of smaller items.

# Terms

## Index 

A collection of objects must be able to be referenced; in most cases this is with an integer known as an <font color="green">index</font>. In Java, the first index is zero (`0`); so, for example, if there are 10 objects in an array, they can be referenced with one of the following: (`0`, `1`, `2`, `3`, `4`, `5`, `6`, `7`, `8`, `9`) (note `10` is _not_ in the list - there are `10` items, but those items are (`0`...`9`)).  

If our array was called `myArrayOfNumbers`, we could reference the second position as `myArrayOfNumbers[1]` (remember the first position would be `myArrayOfNumbers[0]`). 

# Basic Arrays

Here is a short example on how to make an array that holds integers:  
```
//simply declare an array
int[] myArrayOfNumbers; //preferred
int myArrayOfNumbers[]; //works, but not preferred

//actually create the array
myArrayOfNumbers = new int[10]

//you can do both in one step
int[] myArrayOfNumbers = new int[10];

//setting specific elements - the first sets position 0 (usually referred to as index 0) to 77, and the second sets position 8 to 89
myArrayOfNumbers[0] = 77;
myArrayOfNumbers[8] = 89;

//if you want to get the length, you can just use
int myLength = myArrayOfNumbers.length;
```
* Arrays are treated like objects (as they sort of operate like one)
* The different positions in the array are otherwise known as an [index](learn_to_code/java/java_data_structures?id=index) position.  

Here is an example that creates and then immediately sets the values of each position (in the example below, the data is of type `double`, and there are 4 elements in the array).
```
//you can create the array immediately
double[] myDoubleArray = {1.22, 2.3456, 17.578, 99.2};
```

Note that [NULL](learn_to_code/java/java_basics?id=nulls) is represented as `null` and can be checked with basic operators; this can be used when iterating through arrays, specifically checking if the element has been set or not.

# Arrays and Lists


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

> Practically speaking, you should [search](learn_to_code/java/java_data_structures?id=searching-through-arraylists) for the index of the object you wish to remove before you actually remove anything.

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
 * Using [get(0)](learn_to_code/java/java_data_structures?id=basic-arrays) on position 0 would return the element.
 * Using `get(1)` would result in an error.

## Getting Object At Index

This is where an <font color="green">ArrayList</font> really differs from an array - you cannot reference items a la `myVar[x]`, you must use a method to do so.

You can get an object at a specific index using `get()` like so:
```
myVar.get(2)
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* We list a `2`, meaning we want to get the object in position 3 (since the list is based on 0, 0 is considered the first item - so 2 would represent the 3rd, 9 would represent the 10th, etc).

!> If you use `get()` on an index that is out of range - say, the highest index is 10 and you tried to get position 11 - this _will_ error out. To avoid this, always [check the numberof elements in the list](learn_to_code/java/java_data_structures?id=determining-size-of-arraylists); in addition, know that the array is based on 0 but `size()` is _not_ zero-based - so if you use `size()` and it returns a 5, the highest number you can use in `get()` is 4.


## Searching Through ArrayLists

!> The [equals() method **must** be overwritten](learn_to_code/java/java_classes?id=overriding-methods) for searching to work!

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
* This returns the index of the _first_ time the object is seen in the array (you can then use [get()](learn_to_code/java/java_data_structures?id=getting-object-at-index) to interact with the object, now that you have the index).
* If the object does not exist, a -1 is returned.

To get the _last_ position it occupies, use `lastIndexOf()`:
```

myVar.lastIndexOf(myObject)
```
* This returns the index of the _last_ time the object is seen in the array (you can then use [get()](learn_to_code/java/java_data_structures?id=getting-object-at-index) to interact with the object, now that you have the index).
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

Sometimes, you will have to convert your <font color="green">ArrayList</font> to an [Array](learn_to_code/java/java_data_structures?id=basic-arrays); to do so, its:
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

To create a _deep_ copy - which can be modified without changing the original copy - you will have to use an `Interator` class; in addition, you will have to make either an external function or a method in your class that copies the primitives / everything else to another object. I show how t odo this in my [deep copy method](learn_to_code/java/java_classes?id=deep-copy-method).

Here is an example that uses my [School class](learn_to_code/java/java_classes?id=class-example):
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

## ArrayList - Full Example

Here is an example that uses most of the methods available to <font color="green">ArrayList</font>s.  

This example uses my [School class](learn_to_code/java/java_classes?id=class-example):
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

# Looping In Arrays and Lists

It is a necessity to loop through an Array or ArrayList - here are the different ways to do it.

It should be noted that each example here will utilize the [School class](learn_to_code/java/java_classes?id=class-example), with an initial setup as follows (using an ArrayList as an example):.

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

## Traditional Looping

Traditional looping uses a `for` loop, incrementing the index by one each time. Here is a classic example that simply prints the name of each school:
```
	for(int i = 0; i < mySchools.size();i++) {
		System.out.println(mySchools.get(i).getName());
	}
```
* This works for 
 * ArrayLists (as above).
 * Arrays (with some modifications).

## Item: Collection Method 

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

## Lambda Functions 

For ArrayLists, you can use a [lambda expression](learn_to_code/java/java_basics?id=lambda-expression) to loop through, which relies on the `forEach()` method:
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


## Using the Iterator Class (Lists)

You can also use the [Iterator class](learn_to_code/java/java_basics?id=iterator) to loop through every object in the ArrayList:
```
    Iterator<School> cloneIterator = mySchools.iterator();
	while(cloneIterator.hasNext()) {
    	System.out.println(cloneIterator.next().getName());
	}
```
* This works for ArrayLists (as above).
* This does <font color="red">not</font> work for Arrays.
* See the [ListIterator class](learn_to_code/java/java_data_structures?id=listiterator) for more information.

**<font size="4">Testing And Moving The Cursor</font>**

Initially, the cursor points to an area just before the first object (this is done so you must use `next()` to advance initially without weird oddities). You can use the `hasNext()` method (i.e. `myIterator.hasNext()`) to _check_ and see if there is another object after the cursor (note this does not not actually _move_ the cursor); conversely, `hasPrevious()` does the same thing, but backwards (again, it does not actually move the cursor though).

Usually, `hasNext()` or `hasPrevious()` is used in a while loop to _check_ if there are more objects to loop through - if there are, you actually _access_ that object with either `next()` or `previous()` (depending on if you are going backwards or forwards). 

An example (using my [School class](learn_to_code/java/java_classes?id=class-example)):
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

**<font size="4">Manipulating the ArrayList with ListIterator</font>**

Its also possible to _directly_ manipulate the ArrayList through the `iterator` object:
* We can use the `add(myObject)` method of the operator to add `myObject` to the list immediately after the cursor (and, coincidentally, in front of the object that can be accessed with `next()`).
* We can use the `remove()` to remove the current object.
 * This _will_ error out if you have not used `next()` yet _or_ there are no objects in the list.
* We can use the `set(myObject)` to overwrite the current object last returned by a call to either `next()` or `previous()`.

--- 

# Maps  

## Different Types of Maps  

Typically, a `Map` is a lookup - you have the thing you want to look up (a <font color="purple">key</font>) and a <font color="purple">value</font> associated with that <font color="purple">key</font>, For example, some examples of key/value pairs are:  
* Social Security Number / Taxpayer Name  
* Employee ID / Employee Address
* Individual's School ID / Major of Study

More useful is if the <font color="purple">key</font> _uniquely identifies_ an entity and the <font color="purple">value</font> is a _collection of information_ (i.e. what could be an entire JAva [class](/learn_to_code/java/java_classes): 
* Social Security Number / Taxpayer's Tax Return History (which can be an array, list, etc itself).  
* Employee ID / Employee Evaluation.  
* Individual's School ID / History of Student's Classes Taken.  

Most maps can accept a `null` value (with the **exception of** `ConcurrentHashMap`) as either the <font color="purple">key</font> or <font color="purple">value</font>; maps _cannot_ contain duplicate keys (i.e. the keys **must** be unique).  

Java has a few different types of maps: 
* <font color="green">Map</font>
 * Actually `Map` is simply an [interface](learn_to_code/java/java_classes?id=interface) and cannot directly be used.  
* <font color="purple">HashMap</font> (implements `Map`)
 * Good for general use of a `Map` if concurrency or order is needed.  
 * `HashMap` is _not_ thread safe - if you need a thread-safe `Map`, use `ConcurrentHashMap`.  
* <font color="purple">LinkedHashMap</font> (implements `Map`)
 * Is _not_ thread safe - if you need a thread-safe `Map`, use `ConcurrentHashMap`.   
 * Guarantees order of keys via insertion order.  
 * Slightly slower in speed when compared to `HashMap`.  
 * You have the option of setting thie order to 'access order' too, but the default is insertion order (see [the documentation](https://docs.oracle.com/javase/6/docs/api/java/util/LinkedHashMap.html#LinkedHashMap(int,%20float,%20boolean&#41;)).
   * If set to access order, the methods `get()`, `put()`, and `putAll()` _will_ change the order (other method calls will not).  
 * _This seems to be the best pick if you do not have to worry about concurrency but do worry about insertion order_.
* <font color="purple">ConcurrentHashMap</font>
 * Use this instead of a `HashMap` when the `Map` will be utilized in a threaded environment.  
 * There is no guaranteed order for the keys for `ConcurrentHashMap`.  
 * **Cannot** accept a null key (most other `Map` items can).  
* <font color="purple">TreeMap</font> 
 * Is _not_ thread safe - if you need a thread-safe `Map`, use `ConcurrentHashMap`.   
 * Guarantees order of keys via keys sorted by ascending order (whatever `Object` type that is the key, it _must_ have either the `Comparable` or `Comparator` methods overridden).  
 * Considerably slower in speed when compared to `HashMap` (O(log(n)) vs O(1)).  

> For more information on Maps in general, [this page](http://www.javapractices.com/topic/TopicAction.do?Id=65) shows a good comparison of older `Maps`, [here](https://stackoverflow.com/questions/10710193/how-to-preserve-insertion-order-in-hashmap) is a discussion on `HashMap` vs `LinkedHashMap`, [here](https://www.baeldung.com/java-treemap-vs-hashmap) is a resource on `HashMap` vs `TreeMap`, and [here](https://www.java67.com/2012/08/difference-between-hashmap-and-concurrentHashMap-java-collection.html) is a resource on `HashMap` vs `ConcurrentHashMap`.   

!> [This site](https://javarevisited.blogspot.com/2011/04/difference-between-concurrenthashmap.html) claims that knowing the difference between a ConcurrentHashMap vs Hashtable vs Synchronized map is a constant question in Java coding interviews.  Hashtable is legacy and uses synchronized methods to get thread safety - its slow when there are multiple threads accessing it.  Synchronized Map is similar to Hashtable - with the same defects - but its newer and can wrap around any `Map` object.  The best choice for speed is `ConcurrentHashMap` if the `Map` will be accessed by multiple threads, potentially at once.  

## Basic Map Example  

**<font size="4">HashMapPassedReferenceTest.java</font>**  
```
package com.wagenseller;

import java.util.Map;

public class HashMapPassedReferenceTest {

    public static void ReferenceTest(Map<String, String> tempMap, String name, String state) {
        tempMap.put(name,state);
    }
}
```

**<font size="4">Main.java</font>**  
```
package com.wagenseller;


import java.util.*;

public class Main {

    public static void main(String[] args) {

        Map<String,String> myMap = new LinkedHashMap<>();

        myMap.put("Brent","Pennsylvania");
        myMap.put("Rick","Wisconsin");
        myMap.put("Robert","New Jersey");
        myMap.put("Barbara","New Jersey");
        myMap.put("Chris","Illinois");

        HashMapPassedReferenceTest.ReferenceTest(myMap,"Tracy","Georgia");
        HashMapPassedReferenceTest.ReferenceTest(myMap,"Marlow","Pennsylvania");

        //print out the list
        for (String key : myMap.keySet()) {
            String state = myMap.get(key);
            System.out.println(key + " - State: " + state);
        }

        String emptySetTest = myMap.get("");
        System.out.println("Empty Set test returned (should be null): " + emptySetTest);

        String nullTest = myMap.get(null);
        System.out.println("Null test returned (should be null): " + nullTest);

        String removedPersonsState = myMap.remove("Chris");
        System.out.println("Removed Person's State: " + removedPersonsState);

        System.out.println("There are now " + myMap.size() + " people left in the HashMap.");

        //print out the list again - we should be missing the removed person (Chris from Illinois) 
        for (String key : myMap.keySet()) {
            String state = myMap.get(key);
            System.out.println(key + " - State: " + state);
        }
	}
}
```

> The `for` loop basically extracts the pairs, one by one; more on this [here](learn_to_code/java/java_data_structures?id=looping-via-for-maps), and the superior (yet more cumbersome) way [here](learn_to_code/java/java_data_structures?id=looping-via-the-iterator-class-maps). Know that this `for` loop method can potentially error out if an element is removed from the map while its being trafersed, hence [utilizing the Iterator class is recommended](learn_to_code/java/java_data_structures?id=looping-via-the-iterator-class-maps).  


## Advanced Map Example 1

The [basic map example](learn_to_code/java/java_data_structures?id=basic-map-example) simply used a `<String, String>` as the `<key, value>`; this example uses something a bit more advanced (a `<String, ArrayList<HashMapTestClass>>`).  Using a `ArrayList<HashMapTestClass>` allows for an entire array as the <font color="purple">value</font> of a single <font color="purple">key</font> in the original `Map` object. 

> If we wanted to use a different type of object other than `String` as the <font color="purple">key</font>, we would have to make sure that `equals()` and `hashCode()` are overridden for whatever object is used (see [here](learn_to_code/java/java_classes?id=overriding-methods) for more on overriding those two methods).  

**<font size="4">HashMapTestClass.java</font>**
```
package com.wagenseller;

public class HashMapTestClass {

    private int someInt;
    private String someString;

    public HashMapTestClass(int someInt, String someString) {
        this.someInt = someInt;
        this.someString = someString;

    }

    public int getSomeInt() { return someInt; }
    public void setSomeInt(int someInt) { this.someInt = someInt; }

    public String getSomeString() { return someString; }
    public void setSomeString(String someString) { this.someString = someString; }
}

```


**<font size="4">Main.java</font>**  
```
package com.wagenseller;

import java.util.*;

public class Main {

    public static void main(String[] args) {

        Map<String, ArrayList<HashMapTestClass>> myHashMap = new HashMap<>();

        HashMapTestClass lifeEvent;

		//create a new ArrayList that will house our ArrayList of life events
        ArrayList<HashMapTestClass> lifeEvents = new ArrayList<>();

        /////start out with John
		//We need to create a new HashMapTestClass for each lifeEvent, and then we will stuff that onto the ArrayList lifeEvents
        lifeEvent = new HashMapTestClass(1988,"Birth");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2006,"Graduated High School");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2011,"Graduated College");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2016,"Got Scolded");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2017,"Got Married");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2019,"Got New Job");
        lifeEvents.add(lifeEvent);

		//add the key and value (in this case the ArrayList<HashMapTestClass> "lifeEvents") onto our HashMap
        myHashMap.put("John", lifeEvents);

        /////now for Heather
		//We MUST re-initialize lifeEvents, otherwise it will still contain the information from John
        lifeEvents = new ArrayList<>();

        lifeEvent = new HashMapTestClass(1992,"Birth");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2011,"Graduated High School");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2016,"Graduated College");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2017,"Got Married");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2018,"Got New Job");
        lifeEvents.add(lifeEvent);

        myHashMap.put("Heather", lifeEvents);

        /////now for James
        lifeEvents = new ArrayList<>();

        lifeEvent = new HashMapTestClass(2017,"Birth");
        lifeEvents.add(lifeEvent);

        lifeEvent = new HashMapTestClass(2020,"Attended Pre-K");
        lifeEvents.add(lifeEvent);

        myHashMap.put("James", lifeEvents);

        /////now for Rose
        lifeEvents = new ArrayList<>();

        lifeEvent = new HashMapTestClass(2019,"Birth");
        lifeEvents.add(lifeEvent);

        myHashMap.put("Rose", lifeEvents);

        for (String key : myHashMap.keySet()) {
            ArrayList<HashMapTestClass> tempLifeEvents = myHashMap.get(key);

            for(HashMapTestClass oneEvent: tempLifeEvents) {
                if (oneEvent.getSomeInt() == 2016 && oneEvent.getSomeString().equals("Got Scolded")) {
                    oneEvent.setSomeString("Was....corrrrrrrected.");
                }
                System.out.println(key + " - Year: " + oneEvent.getSomeInt() + " Event: " + oneEvent.getSomeString());
            }
        }
	}
}
```

## Defining a Map

> This section uses [the basic map example](learn_to_code/java/java_data_structures?id=basic-map-example).  

We can define a `Map` like so:
```
Map<String,String> myMap = new LinkedHashMap<>();
```

> Neither the <font color="purple">key</font>s - nor <font color="purple">value</font>s - have to be `String` - they can be any object.  

Above, we define the [interface](learn_to_code/java/java_classes?id=interface) as `Map<String,String>` with a <font color="red">(</font><font color="orange">MapInterface</font><font color="purple"><</font>(Key Object Type, Value Object Type)<font color="purple">></font><font color="red">)</font> - in our case, both the <font color="purple">key</font> and <font color="purple">value</font> for each entry will be of type `String`, so we just use `String, String`.  
	
In the interface we said `Map<String,String>`, but notice in the variable definition we said `new LinkedHashMap<>()` - we could have said `new LinkedHashMap<String, String>()` instead, but that would have been redundant (this is how it used to be in Java).  

> Note that we could have used any [interface](learn_to_code/java/java_classes?id=interface) from anything that uses the `Map` interface (a short selection is [here](learn_to_code/java/java_data_structures?id=different-types-of-maps), but we stuck with `Map` as we want to leave our options open for sending this map to a different method without having to [downcast or upcast](learn_to_code/java/java_classes?id=downcasting-upcasting) the interface. 

## Adding to a Map

> This section uses [the basic map example](learn_to_code/java/java_data_structures?id=basic-map-example).  

Each element is placed in the Map with `.put(keyObject,valueObject)`; for the first line in [the basic map example](learn_to_code/java/java_data_structures?id=basic-map-example), we had `myMap.put("Brent","Pennsylvania");`, which created a <font color="purple">key</font> as a String "Brent" and with a <font color="purple">value</font> of "Pennsylvania".  

It should be noted that _if_ the element already exists, it will simply be over-written (and the `.put()` will actually return the _old_ value).  

> Neither the <font color="purple">key</font>s - nor <font color="purple">value</font>s - have to be `String` - they can be any object.  

## Maps Passed by Reference

> This section uses [the basic map example](learn_to_code/java/java_data_structures?id=basic-map-example).  

We passed the map along with a <font color="purple">key</font> and <font color="purple">value</font> (a key of "Tracy" and a value of "Georgia") to the static method `HashMapPassedReferenceTest.ReferenceTest(tempMap,name,state)` in [the basic map example](learn_to_code/java/java_data_structures?id=basic-map-example).  I passed the map to this method to simply show that if the map is passed as a variable elsewhere, it can be modified elsewhere and the changes will be retained once that method call ends (the states for both "Tracy" and "Marlow" persist in the for loop that prints the <font color="purple">key</font> / <font color="purple">value</font>s).  

!> Know that any object stored in multiple maps will persist until it is removed in the final map it belongs to.  

## Values Passed By Reference Inconsistencies


There are times when things are not passed by reference - specifically, using Objects such as `Integer`, `Long`, etc can sometimes revert to their [primitive](learn_to_code/java/java_basics?id=primitive-variables) version (note these are not [primitives](learn_to_code/java/java_basics?id=primitive-variables) but are classes).  For example:
```
Map<Long, Map<String, Long>> nestedMap = new LinkedHashMap<>();
Map<String, Long> otherMap = new HashMap<>();
Long myLong;

otherMap.put("abc", 57l);
nestedMap.put(66l, otherMap);
myLong = nestedMap.get(66l).get("abc");
myLong++;
```

 
To avoid things like this, [Atomic Variables](learn_to_code/java/java_basics?id=atomic-variables) should be used instead of the class version of primitives.  

> For variables that can change, its suggested to use Atomic variables when

## Extracting Map Values

> This section uses [the basic map example](learn_to_code/java/java_data_structures?id=basic-map-example).  

The base way to get the object <font color="purple">value</font> from the map (given the <font color="purple">key</font>) is via the `get()` method:  
```
String state = myMap.get(key);
```

This returns the object as <font color="purple">value</font> (which in the example's case is a String).  A `null` is returned if the key does not exist.  

!> Most map classes can indeed use both a `null` <font color="purple">key</font> and/or <font color="purple">value</font>, with the notable exception of `ConcurrentHashMap`; `ConcurrentHashMap` will throw an error if a `null` key is stored or even searched via [get()](learn_to_code/java/java_data_structures?id=extracting-map-values).  

## Removing Map Values

> This section uses [the basic map example](learn_to_code/java/java_data_structures?id=basic-map-example).  

To extract an object _and_ remove it from the map at the same time we can use `.remove()` as such:  
```
String removedPersonsState = myMap.remove("Chris");
```  
The above attempts to remove <font color="purple">value</font> associated with the <font color="purple">key</font> "Chris" _and at the same time_ will store the object (in our case the <font color="purple">value</font> String) to a variable.  If no  <font color="purple">key</font> exists that matches the given key (in our case "Chris") a `null` is returned.  

!> Most map classes can handle a `null` object as a key (in our example if we did something like `String removedPersonsState = myMap.remove(null)`), but it should be noted that `ConcurrentHashMap` will throw an error if a `null` key is used in a `remove()`.  

## Getting Count of Map Values  

Getting the count of map values is simple: use `.size()`:  
```
myMap.size()
```  

## Looping via the Iterator Class (Maps)

> See the [ListIterator class](learn_to_code/java/java_data_structures?id=listiterator) for more information.  The basis of this discussion was found [here](https://stackoverflow.com/questions/1066589/iterate-through-a-hashmap).  

You can use the [Iterator class](learn_to_code/java/java_basics?id=iterator) to loop through every object in the Map:
```
        Map<Integer,String> myHashMap = new HashMap<>();

        for (int i=0; i<100;i++) {
            myHashMap.put(Integer.valueOf(i), "a");
        }

        Iterator iterator = myHashMap.entrySet().iterator();
        while (iterator.hasNext()) {
            Map.Entry pair = (Map.Entry) iterator.next();
			
			//iterator.remove(); //This is commented out, but you could remove this item here without issue (it is removed from the Map, but the pair variable still holds the key and value safely)
			
            Integer myKey = (Integer) pair.getKey();
            String myValue = (String) pair.getValue();

            System.out.println("Key: " + myKey.intValue() + "   value: " + myValue);
        }
```
* This works for most Maps.
* We use `entrySet().iterator()` to get the iterator for the `Map` (in this case a HashMap).  
* We use the [interface](learn_to_code/java/java_classes?id=interface) `Map.Entry` to interact with each pair.
* We use `getKey()` and `getValue()` to get the key and value (we have to cast the class as their respective classes).  

Initially, the cursor points to an area just before the first object (this is done so you must use `next()` to advance initially without weird oddities). You can use the `hasNext()` method (i.e. `iterator.hasNext()`) to _check_ and see if there is another object after the cursor (note this does not not actually _move_ the cursor).

Usually, `hasNext()` is used in a while loop to _check_ if there are more objects to loop through - if there are, you actually _access_ that object with `next()`. 

Finally, its also possible to remove an element off the map too: the above code has `iterator.remove()` commented out, but that is how it could be done. `iterator.remove()` doesnt return anything; it is removed from the Map, but the `pair` variable still holds the key and value safely.   

!> Using the `Iterator` method is preferred; its also possible to [simply use a for loop](learn_to_code/java/java_data_structures?id=looping-via-for-maps), but that can error out if you attempt to remove anything in the loop (or some other thread removes an item).  

## Looping via For (Maps)

Its also possible to simply loop through the `Map` with a `for` loop like so:  
```
	Map<Integer,String> myHashMap = new HashMap<>();

	// store values
	for (int i=0; i<100;i++) {
		myHashMap.put(Integer.valueOf(i), "a");
	}
		
	// use a for loop to extract each key/pair
	for (Integer key : myHashMap.keySet()) {
		System.out.println("Key: " + key.intValue() + "   value: " + myHashMap.get(key));
	}
```

This is far more straightforward than the [iterator version](learn_to_code/java/java_data_structures?id=looping-via-the-iterator-class-maps), but it comes at a price: if an element is removed from the `Map` while this loops, you *will* have an error thrown (even if its not another thread that removes the current item - an error will be thrown if you try to remove an item in the above for loop itself). This method should _only_ be used if you are absolutely certain that: 
* You will not be removing any elements from the `Map` in the loop.  
* No other process / thread will remove an item from the `Map` while you are looping here. 

## Map Order Test

This function can be placed anywhere and called - it can test to see if the order of one of the various [map types](learn_to_code/java/java_data_structures?id=different-types-of-maps) performs as expected.  
```
    public void testHashMapOrder() {

        int potentialIndex;
        int nextNum;

        Map<Integer, Integer> myHashMap = new LinkedHashMap<>();
        List<Integer> myList = new ArrayList<Integer>();
        int testWorked = 1;

        //fill hashmap
        nextNum = 0;
        for(int i = 0; i < 100; i++) {

            //mix up the indexes just to make sure the index does not matter
            do  { potentialIndex = random.nextInt(1000000); } while (myHashMap.get(potentialIndex) != null);
            myHashMap.put(new Integer(potentialIndex), new Integer(nextNum));
            myList.add(nextNum);
            nextNum++;
        }

        System.out.println("The values were added sequentially in order.");
        for (Integer key : myHashMap.keySet()) {
            System.out.println("key: " + key.intValue() + " ::: value: " + myHashMap.get(key).intValue());
        }
    }
```

---


# ListIterator

!> While there are other ways to [loop through lists and hashMaps](learn_to_code/java/java_data_structures?id=looping-in-arrays-and-lists), its important to understand the ListIterator: if you try to remove objects from the list using any other method other than a ListIterator, you _will_ have an out of bounds exception eventually. If you wish to remove elements from a list or hashMap in a loop, you _must_ use a ListIterator. If you wish to see more in the ListIterator class, [you can check it out here](https://www.tutorialspoint.com/java/java_using_iterator.htm).


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

# Properties Class

Java provides a sleek way to import properties that need to be set in the code with the [Properties](https://www.tutorialspoint.com/java/java_properties_class.htm) class.  This seems to sprinkle some property-specific logic combined with the ability to [read a properties file](learn_to_code/java/java_file_manipulation?id=bufferedreader-files-on-disk) and utilize a [basic Map](learn_to_code/java/java_data_structures?id=maps).  

The idea behind the `Properties` class is there is an endless list of property settings for software - things like: 
* IP Addresses of nodes. 
* Database servers. 
* Login credentials.  
* Variables that should have their default set dynamically (i.e. not hard-coded).  

`Properties` solves the need to have a quick way to storing and retrieving parameters like the above (and others).  `Properties` can: 
* Quickly read properties from a file (on the file system or in the artifact / jar file itself).  
* Quickly gets and sets properties.  
* Allows for defaults to be returned if the property does not exist.  

!> Properties Can only have `String` <font color="purple">key</font>s and `String` <font color="purple">value</font>s!  

> Loading properties from a file is not required, but it probably makes more sense to do so - its possible to to build it with knowledge already available to the code (and just use the `Properties` object as a [singleton](learn_to_code/java/java_classes?id=singleton-classes) as a point of reference), but its true power comes in from getting 'dynamic' variables set from outside of itself.  

## Properties Example

**<font size="4">my.properties</font>**  
```
state=Pennsylvania
job=software engineer
school=Georgia Tech
#this is a test to check out comments!
favoriteFood=Chicken Wings
unusedParameter=what does this even do?
```

**<font size="4">Main.java</font>**  
```
package com.wagenseller;

import java.io.*;
import java.util.Iterator;
import java.util.Properties;

public class Main {

    public static void main(String[] args) {

        String propFile = "my.properties";
        String propFileInJar = "/my.properties";

        BufferedReader bufferedReader = null;

        try {
			// look for "/my.properties" in this project's jar file
            bufferedReader = new BufferedReader(new InputStreamReader(InputStream.class.getClass().getResourceAsStream(propFileInJar)));
            System.out.println("Found file in jar file! Continuing...");
        } catch (Exception e) {
            System.out.println("Warning - could not find file in the JAR file - trying to find on disk....");

            try {
				// the file was not in the jar file, so look in the file system instead
                FileReader fileReader=new FileReader(propFile);
                bufferedReader = new BufferedReader(fileReader);
            } catch (Exception e2) {
                System.out.println("Error - could load file from jar file OR disk!");
            }
        }

        Properties props = new Properties();
        try {
			//load the file into the props object and then close bufferedReader
            props.load( bufferedReader );
            bufferedReader.close();
        } catch (Exception e) {
            System.out.println("Could not read file: " + propFile);
            e.printStackTrace();
        }

		//save a bunch of properties that are from the file - also see what happens with a property that is _not_ in the file
        String myState = props.getProperty("state");
        String myJob = props.getProperty("job");
        String mySchool = props.getProperty("school");
        String myFavoriteFood = props.getProperty("favoriteFood");
        String shouldBeNull = props.getProperty("#this");

        System.out.println("You are in the state of " + myState + ", your job is " + myJob + ", you went to school at " + mySchool + ", and your favorite food is " + myFavoriteFood + ".");

		// if a property is not available, it will return a null in getroperty()
        if (shouldBeNull == null) System.out.println("Property 'shouldBeNull' was null, as expected.");
        else System.out.println("This should never print.");

		// Here is how you would set a default for a property if it did not exist
        String doesNotExistButGetDefault = props.getProperty("doesnotexist", "Stand In");
        System.out.println("'doesNotExistButGetDefault' did not exist, but it defaulted to: " + doesNotExistButGetDefault);

		//here is how to set a new property
        props.setProperty("newValue", "This property has just been set.");
        System.out.println(props.getProperty("newValue"));

        System.out.println("--------------------");

		//simply iterate through all keys
        Iterator myIter = props.keySet().iterator();
        while(myIter.hasNext()) {
            String key = (String) myIter.next();
            System.out.println("Key ::: Value: " + key + ":::" + props.getProperty(key) + ".");
        }
    }
}
```

## Properties File

The properties file is straightforward: its format is <font color="purple">key</font> on the left, an equals `=` sign, and then the <font color="purple">value</font> on the right. An example with three properties in the file:
```
databaseIP=127.0.0.1
login=eo1234
password=somePassword
```

The above example has a <font color="purple">key</font> of `databaseIP` with a <font color="purple">value</font> of `127.0.0.1`, another <font color="purple">key</font> of `login` with a <font color="purple">value</font> of `eo1234`, and a final <font color="purple">key</font> of `password` with a <font color="purple">value</font> of `somePassword`.  

Some other notes:  
* Lines that start with a `#` seem to be comments in the file and are skipped / not loaded to the `Properties` object. 
* The delimiter between the <font color="purple">key</font> and <font color="purple">value</font> is not limited to `=`
 * `whitespace` (such as the space bar) can _also_ delineate between the <font color="purple">key</font> and <font color="purple">value</font> - so the line `is this = for real` would have `is this` as the <font color="purple">key</font> and `= for real` as the <font color="purple">value</font>. **Be careful!**
   * It seems to be whatever is found first, `=` or whitespace (whitespace immediately followed by `=` will work as expected too).  
* Spaces around `=` are ignored.  
* Only the first `=` is read as equals; subsequent eauals signs are lumped in with the <font color="purple">value</font>.  
* The <font color="purple">key</font>s are case-specific.  

## Loading The Properties File

> This section uses [the properties example](learn_to_code/java/java_data_structures?id=properties-example).  

The [properties file](learn_to_code/java/java_data_structures?id=properties-file) can exist in one of two places:  
1\. On the filesystem (which is normal).  
2\. In the jar file of the code. More on how this works [can be found here](learn_to_code/java/java_file_manipulation?id=bufferedreader-files-in-jar) (I suggest you get familiar with how that could work).  

If you wish to load properties from a file, you will need to use [BufferedReader](learn_to_code/java/java_file_manipulation?id=bufferedreader-files-on-disk); its use varies on if you are using a file from source #1 or #2 above.  To get the file from the filesystem you will use something similar to [the properties example](learn_to_code/java/java_data_structures?id=properties-example) in the lines:  
```
FileReader fileReader=new FileReader(propFile);
bufferedReader = new BufferedReader(fileReader);
```  

If, instead, you wished to pull from a properties file in the jar, you would use something like this line from [the properties example](learn_to_code/java/java_data_structures?id=properties-example) instead:  
```
bufferedReader = new BufferedReader(new InputStreamReader(InputStream.class.getClass().getResourceAsStream(propFileInJar)));
```  
* Learn more about how to load a `BufferedReader` object from the jar file [here](learn_to_code/java/java_file_manipulation?id=bufferedreader-files-in-jar). 

Regardless of how the `BufferedReader` object is set, once it is set, load the `BufferedReader` object to the `Properties` object with the line: 
```
props.load( bufferedReader );
```

And, finally, _do not_ forget to close the `BufferedReader` object (as we are now done with it):  
```
bufferedReader.close();
```  

At this point, the `props` object now is loaded with all of our properties from our file!  

!> Its usually suggested that you have the primary method of setting the properties from a file on the filesystem and only reverting to the properties file in the jar as a backup.  

## Adding Properties Manually  

> This section uses [the properties example](learn_to_code/java/java_data_structures?id=properties-example).  

If you wish to manually add a key to the `Properties` object, its `object.setProperty("keyName", "some value here.")'`; in our [example](learn_to_code/java/java_data_structures?id=properties-example) it was done by:  
```
props.setProperty("newValue", "This property has just been set.");
```  

## Retrieving Property Values

> This section uses [the properties example](learn_to_code/java/java_data_structures?id=properties-example).  

If you wish to get the <font color="purple">value</font> of a given <font color="purple">key</font>, you can do so with the `getProperty()` method; as it was done in [the example](learn_to_code/java/java_data_structures?id=properties-example):  
```
String mySchool = props.getProperty("school");
```  
* If the key does not exist, a `null` is returned.  

Sometimes it is not clear if the property will exist in a `Property` object, but you would like a default (i.e. you do not want it to be `null`); if this is the case, you can set a default like so: 
```
String mySchool = props.getProperty("school", "Georgia Tech");
```  
* This sets the default to `Georgia Tech`.  

!> Remember that <font color="purple">key</font>s are case-sensitive!  

## Iterating Through Property Keys

You can use [an iterator](learn_to_code/java/java_data_structures?id=listiterator) to cycle through all keys if you wish; in [the example](learn_to_code/java/java_data_structures?id=properties-example) we did it like so:  
```
Iterator myIter = props.keySet().iterator();
while(myIter.hasNext()) {
	String key = (String) myIter.next();
	System.out.println("Key ::: Value: " + key + ":::" + props.getProperty(key) + ".");
}
```  
* The `key` will need to be [downcasted](learn_to_code/java/java_classes?id=downcasting-upcasting) to a `String`.  

# JSON 

**JavaScript Object Notation** <font color="green">JSON</font> doubles as a file format and a data interchange format. An advantage to it is its a text-based format and is language independent. For more on <font color="green">JSON</font>, see [here](https://www.json.org/json-en.html).  

There are a few different packages that Java uses to interact with <font color="green">JSON</font>.  

## Example of JSON

I like to use a JSON string from the Watson speech results; [Watson](learn_to_code/machine_learning/watson_speech_and_text) is a speech-to-text tool by IBM. It is a good example of JSON as it has a few things happening in it:  
```
{
  "results": [
    {
      "final": true,
      "alternatives": [
        {
          "transcript": "I bless the rains down in Africa ",
          "confidence": 0.99,
          "timestamps": [
            [
              "I",
              2.9,
              3.08
            ],
            [
              "bless",
              3.08,
              3.47
            ],
            [
              "the",
              3.47,
              3.57
            ],
            [
              "rains",
              3.57,
              4.12
            ],
            [
              "down",
              4.17,
              4.47
            ],
            [
              "in",
              4.47,
              4.55
            ],
            [
              "Africa",
              4.55,
              5.22
            ]
          ],
          "word_confidence": [
            [
              "I",
              1.0
            ],
            [
              "bless",
              1.0
            ],
            [
              "the",
              1.0
            ],
            [
              "rains",
              1.0
            ],
            [
              "down",
              0.96
            ],
            [
              "in",
              1.0
            ],
            [
              "Africa",
              0.98
            ]
          ]
        }
      ]
    }
  ],
  "result_index": 0
}
```
* `results` stores most of the information.  
* `final` is a boolean that stores if this particular `results` are the final pass at transcribing the speech (there are multiple attempts made, but there is only one final attempt at transcribing a phrase).  
* `alternatives` is actually an object that houses the following:  
 * `transcript` is the phrase someone spoke that is being transcribed.  
 * `confidence` is a float that ranges from 0 to 1 and represents the % of confidence in the transcription.  
 * `timestamps` is is a double list; the outer list represents a single word in the phrase, in order (for example, there are 7 'timestamps' in the example above). The inner list represents (in this order):  
   * The word itself.  
   * The second (with 0 as the start of the phrase) when the word started.  
   * The second (with 0 as the start of the phrase) when the word ended.  
 * `word_confidence` is is a double list; the outer list represents a single word in the phrase, in order (for example, there are 7 'word_confidence' in the example above). The inner list represents (in this order):  
   * The word itself.  
   * The confidence of this word (so if this is a 1, IBM was 100% sure the word that was supplied by the transcription was the word said by the speaker).  

## GSON 

Google's <font color="green">GSON</font> is a package that can handle coding and decoding JSON strings.  While not always a requirement, you usually must know the structure of the JSON string beforehand, and you must model that structure in a Plain Old Java Object.  

**<font size="4">Maven Import</font>**  

The <font color="green">GSON</font> library must be imported from [Maven](learn_to_code/java/maven); put this in the [dependencies](learn_to_code/java/maven?id=adding-dependencies-to-maven) section of [pom.xml](learn_to_code/java/maven?id=pomxml):  
```
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.8.6</version>
    </dependency>
```  
* The version used above is `2.8.6` but feel free to grab the latest.  

**<font size="4">GSON Example POJO</font>**  

Usually, <font color="green">GSON</font> must have a Plain Old Java object that will mirror the structure if your JSON string; we will use [the example JSON string](learn_to_code/java/java_data_structures?id=example-of-json) as a base. Each keyword in the JSON string _must_ have a corresponding variable name in the POJO class. The example:  
```
package com.wagenseller;

import com.google.gson.annotations.SerializedName;
import java.util.List;

public class WatsonSpeechResults {

    // Watson uses a keyword in Java (final) - this is how to get around that
    @SerializedName("final")
    private boolean isFinal;

    public List<WatsonAlternatives> alternatives;

    public class WatsonAlternatives {

        private String transcript;

        private double confidence;

        private List<List<String>> timestamps;

        private List<List<String>> word_confidence;

        public String getTranscript() { return transcript; }
        public void setTranscript(String transcript) { this.transcript = transcript; }

        public double getConfidence() { return confidence; }
        public void setConfidence(double confidence) { this.confidence = confidence; }

        public List<List<String>> getTimestamps() { return timestamps; }
        public void setTimestamps(List<List<String>> timestamps) { this.timestamps = timestamps; }

        public List<List<String>> getWord_confidence() { return word_confidence; }
        public void setWord_confidence(List<List<String>> word_confidence) { this.word_confidence = word_confidence; }

    }

    public boolean getFinal() { return isFinal; }
    public void setFinal(boolean isFinal) { this.isFinal = isFinal; }
}
```  

**<font size="4">GSON Example</font>**  

Here is an example of how to use <font color="green">GSON</font> using the POJO above:  
```
package com.wagenseller;

import com.google.gson.Gson;

public class GSONExample {

    public static void main(String[] args) {
	
	String         someString = "{\n" +
                "  \"final\": true,\n" +
                "  \"alternatives\": [\n" +
                "    {\n" +
                "      \"transcript\": \"I blessed the rains down in Africa \",\n" +
                "      \"confidence\": 0.99,\n" +
                "      \"timestamps\": [\n" +
                "        [\n" +
                "          \"I\",\n" +
                "          2.9,\n" +
                "          3.08\n" +
                "        ],\n" +
                "        [\n" +
                "          \"blessed\",\n" +
                "          3.08,\n" +
                "          3.47\n" +
                "        ],\n" +
                "        [\n" +
                "          \"the\",\n" +
                "          3.47,\n" +
                "          3.57\n" +
                "        ],\n" +
                "        [\n" +
                "          \"rains\",\n" +
                "          3.57,\n" +
                "          4.12\n" +
                "        ],\n" +
                "        [\n" +
                "          \"down\",\n" +
                "          4.17,\n" +
                "          4.47\n" +
                "        ],\n" +
                "        [\n" +
                "          \"in\",\n" +
                "          4.47,\n" +
                "          4.55\n" +
                "        ],\n" +
                "        [\n" +
                "          \"Africa\",\n" +
                "          4.55,\n" +
                "          5.22\n" +
                "        ]\n" +
                "      ],\n" +
                "      \"word_confidence\": [\n" +
                "        [\n" +
                "          \"I\",\n" +
                "          1.0\n" +
                "        ],\n" +
                "        [\n" +
                "          \"blessed\",\n" +
                "          1.0\n" +
                "        ],\n" +
                "        [\n" +
                "          \"the\",\n" +
                "          1.0\n" +
                "        ],\n" +
                "        [\n" +
                "          \"rains\",\n" +
                "          1.0\n" +
                "        ],\n" +
                "        [\n" +
                "          \"down\",\n" +
                "          0.96\n" +
                "        ],\n" +
                "        [\n" +
                "          \"in\",\n" +
                "          1.0\n" +
                "        ],\n" +
                "        [\n" +
                "          \"Africa\",\n" +
                "          0.98\n" +
                "        ]\n" +
                "      ]\n" +
                "    }\n" +
                "  ]\n" +
                "}";
				
        //The goal now is to save the above as a WatsonSpeechResults object so we can easily interact with it.
        
        //There are two ways you can do this - usually I just use the .class method
        //WatsonSpeechResults info = new Gson().fromJson(someString, new TypeToken<WatsonSpeechResults>(){}.getType());
        WatsonSpeechResults info = new Gson().fromJson(someString, WatsonSpeechResults.class);

        if (info.getFinal()) {
            System.out.println("Transcript: " + info.alternatives.get(0).getTranscript());
            System.out.println("Confidence: " + info.alternatives.get(0).getConfidence());
            System.out.println("First Timestamp: " + info.alternatives.get(0).getTimestamps().get(0));
            System.out.println("First Word in First Timestamp: " + info.alternatives.get(0).getTimestamps().get(0).get(0));
            System.out.println("Start time in First Timestamp: " + info.alternatives.get(0).getTimestamps().get(0).get(1));
            System.out.println("End time in First Timestamp: " + info.alternatives.get(0).getTimestamps().get(0).get(2));
            System.out.println("Last word in word_confidence: " + info.alternatives.get(0).getWord_confidence().get(6).get(0));
            System.out.println("Last word confidence in word_confidence: " + info.alternatives.get(0).getWord_confidence().get(6).get(1));
        }

        //now simply print out the JSON string, using the object 'info'
        Gson gson = new Gson();
        System.out.println(gson.toJson(info));
}
```  