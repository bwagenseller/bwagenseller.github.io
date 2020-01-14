# Learning To Code: Java

# Before We Begin

Arrays, lists, and hashMaps are collections of [objects](learn_to_code/java/java_classes?id=class-vs-object). These are very useful when you need to store a collection of smaller items.

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
* The different positions in the array are otherwise known as an [index](learn_to_code/java/java_lists_arrays_hashmaps?id=index) position.  

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

> Practically speaking, you should [search](learn_to_code/java/java_lists_arrays_hashmaps?id=searching-through-arraylists) for the index of the object you wish to remove before you actually remove anything.

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
 * Using [get(0)](learn_to_code/java/java_lists_arrays_hashmaps?id=basic-arrays) on position 0 would return the element.
 * Using `get(1)` would result in an error.

## Getting Object At Index

This is where an <font color="green">ArrayList</font> really differs from an array - you cannot reference items a la `myVar[x]`, you must use a method to do so.

You can get an object at a specific index using `get()` like so:
```
myVar.get(2)
```
* `myVar` is the variable name of the previously defined <font color="green">ArrayList</font>.
* We list a `2`, meaning we want to get the object in position 3 (since the list is based on 0, 0 is considered the first item - so 2 would represent the 3rd, 9 would represent the 10th, etc).

!> If you use `get()` on an index that is out of range - say, the highest index is 10 and you tried to get position 11 - this _will_ error out. To avoid this, always [check the numberof elements in the list](learn_to_code/java/java_lists_arrays_hashmaps?id=determining-size-of-arraylists); in addition, know that the array is based on 0 but `size()` is _not_ zero-based - so if you use `size()` and it returns a 5, the highest number you can use in `get()` is 4.


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
* This returns the index of the _first_ time the object is seen in the array (you can then use [get()](learn_to_code/java/java_lists_arrays_hashmaps?id=getting-object-at-index) to interact with the object, now that you have the index).
* If the object does not exist, a -1 is returned.

To get the _last_ position it occupies, use `lastIndexOf()`:
```

myVar.lastIndexOf(myObject)
```
* This returns the index of the _last_ time the object is seen in the array (you can then use [get()](learn_to_code/java/java_lists_arrays_hashmaps?id=getting-object-at-index) to interact with the object, now that you have the index).
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

Sometimes, you will have to convert your <font color="green">ArrayList</font> to an [Array](learn_to_code/java/java_lists_arrays_hashmaps?id=basic-arrays); to do so, its:
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


## Using the Iterator Class 

You can also use the [Iterator class](learn_to_code/java/java_basics?id=iterator) to loop through every object in the ArrayList:
```
    Iterator<School> cloneIterator = mySchools.iterator();
	while(cloneIterator.hasNext()) {
    	System.out.println(cloneIterator.next().getName());
	}
```
* This works for ArrayLists (as above).
* This does <font color="red">not</font> work for Arrays.
* See the [ListIterator class](learn_to_code/java/java_lists_arrays_hashmaps?id=listiterator) for more information.


---

# ListIterator

!> While there are other ways to [loop through lists and hashMaps](learn_to_code/java/java_lists_arrays_hashmaps?id=looping-in-arrays-and-lists), its important to understand the ListIterator: if you try to remove objects from the list using any other method other than a ListIterator, you _will_ have an out of bounds exception eventually. If you wish to remove elements from a list or hashMap in a loop, you _must_ use a ListIterator. If you wish to see more in the ListIterator class, [you can check it out here](https://www.tutorialspoint.com/java/java_using_iterator.htm).


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

## Manipulating the ArrayList with ListIterator

Its also possible to _directly_ manipulate the ArrayList through the `iterator` object:
* We can use the `add(myObject)` method of the operator to add `myObject` to the list immediately after the cursor (and, coincidentally, in front of the object that can be accessed with `next()`).
* We can use the `remove()` to remove the current object.
 * This _will_ error out if you have not used `next()` yet _or_ there are no objects in the list.
* We can use the `set(myObject)` to overwrite the current object last returned by a call to either `next()` or `previous()`.

