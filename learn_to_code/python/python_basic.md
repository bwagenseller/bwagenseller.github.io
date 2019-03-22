# Learning To Code: Python

# Before We Begin

Much of this comes in the form of code examples I picked up throughout my time learning Python.

## Assumed System

This document assumes you are working on an [Ubuntu operating system](https://www.ubuntu.com/download) (a bit more specifically, version 18.04); generic install instructions [can be found here](ubuntu/server_build). You can do this on Windows or Mac as well, but the install will be very different and the directory structure will be different for files.

## Python Anaconda Installation

This document will use much of scipy in Python as well as MySQL; scipy is easily installed with the [Anaconda](https://www.anaconda.com/) version of Python, and the install instructions are [here](ubuntu/server_build?id=python-anaconda-install); its also advisable to install [other Python supporting packages](ubuntu/server_build?id=python-supporting-packages) as well, as many of them are referenced here.

# Basics of Python

## Shebang Line

The shebang line begins with a `#!` and is followed by the absolute path of the version of Python you with to run (i.e. where it was installed, either the default location or [if you installed Anaconda](ubuntu/server_build?id=python-anaconda-install). This has to be the very _first_ line in the script. In the following example we use the Anaconda installation which was installed in `/usr/bin/anaconda/python2.7/bin`:

```
#!/usr/bin/anaconda/python2.7/bin/python
```

Now if you want to run a python script (say, learn.py) you can simply type `./learn.py` (if its in the working directory) or the absolute path `/home/brent/python.py`; if you did not have the shebang line you would have to type `python /home/brent/python.py`.

!> The file MUST be set to executable via [chmod](ubuntu/linux_notes?id=changing-file-permissions).

## Python Comments

If you wish to comment a line in Python (which makes the line non-actionable code), you put a `#` in the beginning of the code like so:
```
#This line will not be interpreted by Python
#http://stackoverflow.com/questions/675442/comment-out-a-python-code-block
```

You can also put it elsewhere and anything after it (on that specific line) will not be run as well:
```
a = 1 #python executes a = 1 but nothing after the #, so this messsage itself will not execute
```

Unfortunately comments in Python can only be done with a `#` symbol - you cannot block out large swaths of code with `/*  */` or `=for comment` ... `=cut` (as in Perl).

You can also use something called a [docstring](http://en.wikipedia.org/wiki/Docstring), which is code between """ and """ that is not parsed by the compiler / script.  For example:
```
"""
This is a comment in docstring.
The purpose of a docstring is to document what a file, class, method, or function does. It can also be used to make a long string you want to save to a variable readable in the code,
as it can span multiple lines.

Note docstrings are not necessairly used for comments - unlike comments, doscstrings are not stripped from the code so they can be used outside of the file for documentation purposes.  
Try to avoid using docstrings to block out large swaths of code.  It can be done but can get confusing, as the commented section will show up in generated documentation.

This is how you end a docstring:
"""
```

Info on why its not a fantastic idea to use and also as to why not use a docstring as a comment block [can be found here](http://stackoverflow.com/questions/675442/comment-out-a-python-code-block).

## Main Function

if you want a Python script to run on its own, you need a 'Main' class defined like so:
```
if __name__ == '__main__': 
```

This acts like a function and will run anything under it; if you want to run anything in the code, you must kick that code off under this section.

## Printing To Screen

To print to the screen you use the `print` function. Example:
```
print "Hello, world!"
```

> Notice no semicolin at the end of the line! Python does not usually use semicolins.

## Example Program

Here is an example of a very simple Python program:

```
#!/usr/bin/anaconda/python2.7/bin/python

if __name__ == '__main__': 
	print "Hello, world!"
	
```

> Your [shebang](learn_to_code/python?id=shebang-line) line may need to be altered if Python is located elsewhere in your machine.

## Tabs and Whitespaces

Python does not use curly braces `{  ... }` to define code blocks (if / for / while / try statements, functions, etc); instead, it uses whitespaces (usually 'tab'). This is also true about the [main](learn_to_code/python?id=main-function) function as well. Example:
```
if __name__ == '__main__': 
	print "Hello, world!"
	print "Here is another line"
	
	a = 1
	if (a ==1):
		print "This line will print if a = 1"
	else:
		print This line will print if a != 1"
		a = 2
	
	print "'a' will never equal 2 here, as the extra whitespaces before 'a=2' above happen in the 'else' block, so it will not be set unless the 'else' block runs which it never will in this code"
	
print "This line will cause an error"
```

The first two print statements will work fine. Notice the specific code that will work i fthe 'if' statement is true gets another indent, and the 'else' code block gets the same indent (but the actual word 'else' gets as big of an indent as the 'if' statement). 

You can have _more_ if you are in an if/for/while/etc code block, but you can never have _less_ than one tab (or block of whitespaces), and if you decrement the size of the whitespace, you are exiting that code block.

Note that the last print statement will error out as the whitespaces in that line indicate it has exited the main function.

> What exactly defines a leading whitespace? Usually a tab, sometimes multiple spaces. Just know that whatever is used, it has to be *consistent* (so you can't mix tabs and multiple spaces in the same file).

!> Sometimes when porting code to work on another machine, the whitespace structure is different; if you notice the code lines up but its failing, check to see if your whitespace choice matches what was used previously!

## Importing Modules

You will almost always have to use functions that are not in the bare bones of Python; when this is the case (and it usually is), you must import the module you need like so (just after the  [shebang](learn_to_code/python?id=shebang-line) line but before any code) (this example imports the re, string, and sys modules):
```
import re
import string, sys
```

> If you are using an IDE, the available functions in modules will be listed after you type a dot (so typing 'sys.' in an IDE will then list all functions in the 'sys' module). Sometimes you cannot work in an IDE; if this is the case, you can still print out everything in the module with the dir() command like so: `print dir(sys)`. This will print a list of all available functions in that module.

You can also rename a module on a temporary basis in your code; for example, if you wanted to import the sys module but thought the word 'sys' was too long, you cod do something like this:
```
import sys as s
```

Now in your code, anywhere you would reference 'sys' you could just use 's' instead. An example is the command to exit the code immediately; usually the command for this is `sys.exit(0)`, but if you renamed sys as 's' you would then use `s.exit(0)` instead.


## Running a Python Script

To run a python script, you must have a [main function](learn_to_code/python?id=main-function); after that, if you set-up the [shebang](learn_to_code/python?id=shebang-line) correctly and set the [file's permissions]((ubuntu/linux_notes?id=changing-file-permissions)) to executable, simply open a command line and (assuming our script is called 'learn.py' in the directory '/home/brent') run the command `./learn.py` (if its in the working directory) or the absolute path `/home/brent/python.py`; if you did not have the shebang line set-up correctly, you would have to type `python /home/brent/python.py`.

## Exiting a Script Imediately

If in your code you need to exit a script immediately, you can use `sys.exit(0)` from the sys [module](learn_to_code/python?id=importing-modules). Note that you do not have to usually use this to exit a script, its just for odd cases.

---

# Basic Variables

**<font size="4">Strings</font>**

I will talk much more about strings [here](learn_to_code/python?id=strings), but for now, just know that strings are a collection of characters that usually form words, and they are wrapped in double or single quotes. For example, these are 4 strings:
```
"Ted Scheckler"
"867-5309"
'Be better than me, Morty.'
"72"
```

Strings are just characters (even numbers) that are between double or single quotes.

**<font size="4">int</font>**

int (Integers) are just regular whole numbers, positive or negative.

**<font size="4">long</font>**

Long numbers are integers that are much larger than ints. These can also be hexadecimal or octal.

**<font size="4">float</font>**

Floats are real numbers.

**<font size="4">complex</font>**

Complex are comples numbers (937e-78j, 4e+45J, etc)

**<font size="4">Converting Basic Variables to Other Basic Variables</font>**

Often times you will find that you need one type of variable (say a string), and you need another (say an int); this is particularly useful for converting strings (that are fully comprised of integers) into an actual integer (as if you tried to use it as an integer without the conversion, it will fail). There are a few conversion functions to do this:
* `str()` - converts whatever is in the `()` to a string.
* `int()` - converts whatever is in the `()` to an integer.
* `float()` - converts whatever is in the `()` to a float.
* `long()` - converts whatever is in the `()` to a long.
* `complex()` - converts whatever is in the `()` to a complex.

Examples:
```
pi = "3.14"
myNum = float(pi) + 1
print myNum #prints 4.14
	
bigNum = "57643333"
myNum = long(bigNum) + 1
print myNum 
```

# Tuples

A 'tuple' is a collection of numbers or strings. Note that a tuple is immutable, meaning once its created it cannot be modified.

Example:
```
#Tuples can be a list of most native types. The variable 'myTuple' is below
myTuple = (77, 56, 'Bob', 'Curly') 
```

You can reference a _specific_ tuple with brackets as such: `myTuple[2]`. In this case, 'Bob' will be returned. [See here](learn_to_code/python/python_basic?id=referencing-elements-in-variables) on more about how to do this.

# Lists

Lists are much like tuples with one major caveat: you can add (and remove) items from a list. In addition, lists can be any combination of numbers, strings, and even tuples!

You can initalize a list by setting it equal to `[]` (for example, `myList = []`) or you can populate it (see example below). When telling the difference between a tuple and a list, the visual clue is tuples are initialized with `()` and lists are initialized with `[]`.

Here is an example of a list (see [here](learn_to_code/python/python_basic?id=referencing-elements-in-variables) if you do not understand how to reference specific elements in a tuple or list; also note that [multidimensional lists](learn_to_code/python/python_basic?id=multidimensional-lists) are used as well):
```
myFirstList=[(1,0,3), (5,6,7), ('Johnny', 'Cash', 'it', 'aint', 'me', 'babe', 1965), ('Andy', 'woody', 'buzz')]
print "The length of the list is " + len(myFirstList)
print "The length of the 3rd element (position 2) is: " len(myFirstList[2])
print "The first element of the 3rd tuple (position 2) is: " myFirstList[2][0]
```

## Appending Lists

While its true that tuples are immutable, lists are not.  This means if you need to add a new element to a list, you can with the `.append[]` method:
```
myFirstList=[(1,0,3), (5,6,7), ('Johnny', 'Cash', 'it', 'aint', 'me', 'babe', 1965), ('Andy', 'woody', 'buzz')]

newList = [] #initialize the temp list
newList.append('each') #add an element to the list
newList.append('time')
newList.append('you')
newList.append('fall')
myFirstList.append(newList) #append the new list to the original list!
print "The newest tuple added is " myFirstList[len(myFirstList)-1]
```

## Appending vs Extending

We saw above that we can add an element onto a list with the `.append()` method; however, what if we simply wanted to concantenate two lists?  See this example:
```
confusingList = ['a', 'b', 'c', 'd']
newList = ['e', 'f', 'g']
	
confusingList.append(newList)
print confusingList
```

`confusingList` is `['a', 'b', 'c', 'd', ['e', 'f', 'g']]`; this isn't exactly what we wanted, as e/f/g is not added onto the end, but rather, the _list_ `['e', 'f', 'g']` is added.

In order to concantenate lists, we can use `.extend()` like so:
```
anotherConfusingList = ['a', 'b', 'c', 'd']
newList = ['e', 'f', 'g']
	
anotherConfusingList.extend(newList)
print anotherConfusingList
```
This makes `['a', 'b', 'c', 'd', 'e', 'f', 'g']`, which is what we wanted.

## Inserting into Lists

`.append()` can add an element to the end, but what if we wanted to put something in a specific spot? We could use the `.insert()` method.

For example, if we had a list ['a', 'c', 'd'] and we wanted to put a 'b' in the second position, we could do so like this:
```
myList = ['a', 'c', 'd']
myList.insert(1, 'b')
print myList
```

## Popping From Lists

We can 'pop' an item off a list (and into a vairable). This removes the item from the list. If we specify a number to pop, it will pop the element in that position; if we do not, it will simply pop the last element.

Example:
```
myList = ['a', 'b', 'c', 'd']
myFirstLetter = myList.pop(1)
mySecondLetter = myList.pop()
print myFirstLetter #will print 'b'
print mySecondLetter #will print 'd'
print myList #will print the remaining list which is ['a', 'c']
```

## Reversing Lists

Its possible to reverse the ordering of a list with the `.reverse()` method; this method works in place (meaning, you just call it without setting anything equal to it). For example,
```
myList = ['a', 'b', 'c', 'd']
myList.reverse()
print myList #prints ['d', 'c', 'b', 'a'] to the screen
```

## Comparing Lists

You can compare lists with the `cmp()` function. This seems to see if the elements in the list are the same (and in the same positions); if they are equal a zero is returned, otherwise a 1 or -1. Exmple:
```
myListA = ['a', 'b', 'c', ['d', ['e'], 'f']]
myListB = ['a', 'z', 'c', 'd']
myListC = ['a', 'b', 'c', ['d', ['e'], 'f']]
myListD = ['a', 'b', 'c', ['d', 'e', 'f']]
myListE = [['d', ['e'], 'f'], 'a', 'b', 'c']
	
print cmp(myListA, myListB) # prints -1
print cmp(myListA, myListC) # prints 0
print cmp(myListA, myListD) # prints -1
print cmp(myListA, myListE) # prints 1
```

## Counting a Specific Object

You can count how many times a _specific_ object appears in a list with `.count()`. Example:
```
myList = ['z', '', 'a', 'z', 'c', 'd', 1, '1', ['quad', 'core']]
	
print myList.count('z') #prints 2, as there are two 'z's
print myList.count(['z']) #prints 0, as this is a list and not just a z

print myList.count(1) #prints 1, as there is only one 1 (the other '1' is considered to be a string)

print myList.count('quad') #prints 0
print myList.count(['quad', 'core']) #prints 1
```

## Finding the Min/Max of a List

There is an easy way t ofind the min (or max) of a list:
```
myList = ['b', 'a', 'z', 'c', 'd']
print min(myList) #prints 'a'
print max(myList) #prints 'z'
```

This can get tricky though if you mix numbers and letters; also know that the string representation of `"1"` is NOT the same as `1`; the string representation will always come _after_ the numerical representation. For example:
```
myList = ['1', 2, 3, 4, '5', 6]
print min(myList) #prints 2, as its the lowest number that is not a string
print max(myList) #prints '5', as even though there is a 6 the '5' is a string and strings come after integers when ordering
```

!> Be *very* careful when ordering strings that you think are numbers!

Finally, multidimensional lists can get weird. Example:
```
myList = [[['b', 'w'], ['e', 'z']], ['a', 'a'], 'h']
print min(myList) #prints [['b', 'w'], ['e', 'z']]
```

It seems to investigate the first dimension, and then returns the dimension that has the most dimensions, and actually compares ties. So our above example yields `[['b', 'w'], ['e', 'z']]` but if we made the `['a', 'a']` into `[['a', 'a']]` it would select `[['a', 'a']]` instead.

## List Comprehension

Its possible to perform an action on each element of a list and then return a list of the results in one line. List comprehension is just a fancy way to perform an action on each element in a list all within one line.

The example below starts out with a list of dates, then we use list comprehension to convert all dates to a readable string and store that to a new list.

```
listOfDates = [dt.datetime(2019,02,01), dt.datetime(2019,02,02), dt.datetime(2019,02,03), dt.datetime(2019,02,04), dt.datetime(2019,02,05)]
stringOfDates = [d.strftime('%Y-%m-%d') for d in listOfDates]
print stringOfDates

# Prints to the screen ['2019-02-01', '2019-02-02', '2019-02-03', '2019-02-04', '2019-02-05']
```
* Note the use of 'd', which is a variable created on the fly. Its required to reference each item in the list.
 * Basically, a for loop is traversed, stuffing the entries in listOfDates into the variable 'd' one by one; this means, in turn, that d.strftime() is incrementally built.

In the above, 'stringOfDates' is a list of dates (in string format).

> Its also quite possible to run each element of a list through a function using this method.

## Zipping Lists

Its possible to _quickly_ 'combine' lists (or `numpy.ndarray`), creating a list of tuples. This is helpful in machine learning instances, where you have the features in one list / numpy array and the labels in another and you want to join them for weight updating.

Example:
```
features = [[1, 10], [2, 20], [3, 30]] 
labels = ['one', 'two', 'three']
ML_Dataset = zip(features, labels)
	
for xList, y in ML_Dataset:
	print xList
	print y
		
print type(ML_Dataset)
print type(ML_Dataset[0])
```

The above prints the following:

> [1, 10]  
> one  
> [2, 20]  
> two  
> [3, 30]  
> three  
> `<type 'list'>`  
> `<type 'tuple'>`  

---

# Strings

Strings are usually readable characters that are not numbers (although numbers can be a string as well, strings are usually not numbers, but they can be). Strings usually need to be encapsulated in double quotes OR single quotes - just make sure to use them in pairs and there is no mixing (so for example, you could say `myName = "Bob"` or `myName = 'Bob'` but `myName = 'Bob"` or `myName = "Bob'` would generate an error). If you ever need to actually print a single or double quote, you can use this to your advantage and use the opposite (for example you can print a ' if you use double quotes as the wrap, i.e. `myMessage = "Don't do that!"`).

## Concatenating Strings

You can concatenate two strings with the `+` operator; here is an example of how to concatenate strings:
```
fName = "Brent"
lName = "Wagenseller"

myConcatenatedString = fName + " " + lName + " has two dogs"
print myConcatenatedString
```

The above variable 'myConcatenatedString' is actually concatenated by 4 strings: fName, lName, " ", and " has two dogs".

## Formatting Strings

Sometimes you do not want to mess with the concatenation method and want to avoid all of the `+` symbols; if this is the case you can use the .format() property of all strings. 

Example:
```
fName = "Brent"
lName = "Wagenseller"
age = 38

myString = "{} is my first name, {} is my last, and I am {} years old".format(fName, lName, age)  
print myString
```

Note that `{}` is a variable placeholder, and they match up to its corresponding variable in the format() section. Also note that you always use `{}`, regardless of if its a string, number, etc.

## Stripping a Character From String


Its possible to strip a specific character (or characters) from the ends - and *only* the ends - of a string with the `.strip()` method:
```
myVar = "abcde"
myVar = myVar.strip('a')
print myVar # prints "acde"
```
> Without an argument, all whitespace is stripped from both ends.

Note that this only works on the _ends_ of a string; so if we stripped 'b' out of the above, nothing would have been done to the string as 'b' is in the middle.

If we only wanted to strip from the right or left, we would use `.rstrip()` and `.lstrip()` respectively. 

## Replacing a Substring

The string.replace function replaces strings in strings.  This replaces 'Bob' with 'Robert':
```
oldName = "Bob"
newName = "Robert"
sentence = "Bob was walking in the park when be saw people bobbing for apples."
newSentence = string.replace(sentence, oldName, newName)
print sentence
print newSentence
```
Note that this is case sensitive - so 'bobbing' does not become 'Roberting'.

## Splitting a String

Sometimes you will need to split a string on a character (particularly lines imported from a csv, but there are other cases when you will have to do this). To do this you use the split method available to strings. Example:
```
myStringWithCommas = "a,b,c,d,e,f,g"
myNewListOfStrings = myStringWithCommas.split(',')
print "The first string is {} and the last is {}".format(myNewListOfStrings[0], myNewListOfStrings[len(myNewListOfStrings)-1])
```

The above has a list of characters separated with a comma. We then use the split(',') method (specifying we are using ',' as a separator); the results are stored in a data type called a list, and we can access any specific element in that list (so myNewListOfStrings is a list, but the first element in that is myNewListOfStrings[0] which is actually a string). The list will have 7 elements (but since list indexers start with a 0, the last element is not myNewListOfStrings[7] but is actually myNewListOfStrings[6]).

## Joining a String

You can take a [list](learn_to_code/python/python_basic?id=lists) and join all elements with some other string.

For example, if we had a list and wanted to join all elements in that list separated by a ":" we would do so like this:
```
myList = ['a', 'b', 'c', 'd', 'e']
print string.join(myList, ":") 
```

This prints `a:b:c:d:e`.

!> The list MUST contain _only_ strings - you cant have other nonstring elements (like numbers or other lists) in the list!

## Regular Expressions in Strings

Regular expressions are used for pattern matching in strings. The topic is huge so I won't get into it here, but you can read more about their use in Python [on tutorialspoint](https://www.tutorialspoint.com/python/python_reg_expressions.htm). The upside is almost every programming language uses regular expressions, and outside of a few slight modifications, regular expressions are the same from language to language. We use the module `re` if we want to use regular expressions.

For example, if we read in a phone number that had a 'comment' in the string - and wanted to eliminate the comment and everything after it - we could do it like so:
```
phone = "2004-959-559 # This is Phone Number"

# Delete Python-style comments
num = re.sub(r'#.*$', "", phone)
print "Phone Num : ", num
```

We took the string "2004-959-559 # This is Phone Number" and changed it to "2004-959-559 " using the 're.sub' function from the `re` module.
* Notice the bare letter 'r' - that signifies 'replace'

**<font size="4">Remove Anything Other Than Digits</font>**

This example just shows how to remove any character that is not a digit:
```
phone = "2004-959-559 # This is Phone Number"
num = re.sub(r'\D', "", phone)    
print "Phone Num : ", num
```
---

# Dates / Datetimes

## Libraries

The main library for datetimes is `datetime`.

## Initializing Times

`datetime.datetime(YYYY,MM,DD)` can be used to set initial dates. The following sets startDate to '2019-02-02':  

```
birthday = datetime.datetime(2019,02,02)
```
Its also possible to simply [convert a string representation of a datetime to a datetime object](learn_to_code/python/python_basic?id=converting-times).

## Converting Times

Its possible to transform a string representation of a time into a `datetime` object; to do so, the object `datetime.datetime.strptime()` is used.  Note that the function uses a mask;  [this website](http://strftime.org/) lists all of the possible elements of the mask, but the standard is `%Y-%m-%d %H:%M%S` but of course there are many options.

Here is a simple example that takes a datetime (in string format) and converts it to a datetime object:
```
myStringDate = '2014-03-22 01:23:17'
myDateObject = dt.datetime.strptime(myStringDate, '%Y-%m-%d %H:%M:%S')
```

## Formatting Times

In order to format a datetime object, the `strftime` function must be used, which uses a mask; [this website](http://strftime.org/) lists all of the possible elements of the mask, but the standard is `%Y-%m-%d %H:%M%S` but of course there are many options. 

Here is a simple example that sets a date and then saves it to a variable in string format:
```
myDate = datetime.datetime(2014,03,22,1,23,17)
stringDate = myDate.strftime('%Y-%m-%d %H:%M:%S')
print "The date is '{}' and the type is '{}'.".format(stringDate, type(stringDate).__name__)
```


---
# Referencing Elements in Variables

## Basic Element Referencing

There is a way to reference any element in a tuple, list, and many other objects by their position number; simply put brackets `[]` at the end of the variable and put the position number you want in the brackets. Observe this code snippet:

```
myTuple = (77, 56, 'Bob', 'Curly')
print myTuple[3]
```
This will print 'Curly'.

Why does it print 'Curly' and not 'Bob', as 'Bob' is clearly the 3rd element? Its because the reference does not start at 1, it starts at 0: so `print myTuple[0]` would print `77` and `print myTuple[4]` would give us an error (as it is out of range).

You can also 'slice' any variable/object that can be referenced like this by specifying a begining and an ending like so: `[beginning:ending]`; leaving either beginning or the ending blank implies you want to start from the beginning or go to the end. For example, please observe the following:
```
myTuple = (77, 56, 'Bob', 'Curly', 'Morty', 'Rick', 42)
print myTuple[0:3] ###This prints '(77, 56, 'Bob')'
print myTuple[0:] ###This prints '(77, 56, 'Bob', 'Curly', 'Morty', 'Rick', 42)'
print myTuple[:3]###This prints '(77, 56, 'Bob')'
print myTuple[2:5]###This prints '('Bob', 'Curly', 'Morty')'
```

> Note that the beginning is inclusive (meaning, it will use that item) and the ending is exclusive (meaning, it will use up to - but not including - that item).

<span style='width: 20px; display:inline-block'></span>
> You can indeed use the 'real' count of elements in the ending slot; for example, `print myTuple[2:7]` is valid, even though `print myTuple[7]` is not (this is due to the range being exclusive of the endpoint).

## Multidimensional Lists

Lists (and other data types) can be multidimensional; this means instead of just one `[]`, you can have many `[]`, with one being nested in the last.

For example, here is a two dimensional list:

```
my2DList = [['a','b','c'], ['d','e','f']]
print my2DList[0][1]
```
If you look closely, there are actually two lists defined in a larger list. The `[0]` in `my2DList[0][1]` references the first collection (`['a','b','c']`), and the `[1]` references the second position (because we start with zero) in that list (which is 'b').

There can be multiple dimensions, many more than two - and they dont have to have the same number of elements! Here is an example:
```
confusingList = [[['a', 'b'], ['c', 'd', 'e', 'f']], ['g', 'h', 'i', 'j', 'k']]
print confusingList[0][0][1] #prints 'b'
print confusingList[0][1][3] #prints 'f'
print confusingList[1][3] #prints 'j'
```

In the example above, the first element is a collection of two other elements: `[['a', 'b'], ['c', 'd', 'e', 'f']]`. The first element of that is `['a', 'b']` and the second is `['c', 'd', 'e', 'f']`. 

## Getting a Count of Elements

To get a count of the number of elements in a tuple, list, or many other elements, simply use `len()`; for example, here is how we can get a count of elements above:
```
myTuple = (77, 56, 'Bob', 'Curly', 'Morty', 'Rick', 42)
print len(myTuple)
```

Remember that since Python starts counts a 0, we could not say something like `print myTuple[len(myTuple)]` to get the last element; instead, we would have to say `print myTuple[len(myTuple)-1]`.

# Dictionaries

## Dictionary Basics

Dictionaries are sort of like lists, but they are geared to storing information using human-recognizable keys. They need to be initialized using `{}`, but after that you can create a key (and data) at will.  Example:
```
myTable = {}
myTable['book'] = '1984'
myTable['HS'] = 'Pennsbury'
myTable['son'] = 'Jackson'
myTable['zero'] = 0
myTable['Qzero'] = '0'
myTable['blank'] = ''
myTable['wife'] = 'Mandi'

print "My wife's name is {}.".format(myTable['wife'])

myID = 'book'
print "My book is {}.".format(myTable[myID])
```

We see that we can name the keys pretty much any alphanumeric string; the cool part about it is, you can use variables in the names for references (as we did with `myID` above).

## Cycling Through Dictionary Keys

We can cycle through _all_ keys _and_ their values with a `for` loop (and using the `.iteritems()` method):
```
for key, myValue in myTable.iteritems():
	print "(key) {}:  {}".format(key, myValue)
	if myValue == '' or myValue == 0: print "BLANK"
```

Or, if you just want to cycle through the keys:
```
for key in myTable:
	print "The key is {} and the value is {}".format(key, myTable[key])
```

## Checking If Dictionary Key Exists

If you want to check to see if a specific key exists:
```
if 'book' in myTable: print "The 'book' key exists!"
```

## Dictionaries in Dictionaries

You can also make a dictionary of dictionaries (infinitely, if you wanted to). Here is how you can do so:
```
myDictionaryOfDictionaries = {}

myDictionaryOfDictionaries['First'] = {} #its important that you initialize the inner dictionary
myDictionaryOfDictionaries['First']['State'] = 'Pennsylvania'
myDictionaryOfDictionaries['First']['City'] = 'Gilbertsville'
myDictionaryOfDictionaries['First']['KnownFor'] = 'Cheesesteaks'

myDictionaryOfDictionaries['Second'] = {} #its important that you initialize the inner dictionary
myDictionaryOfDictionaries['Second']['State'] = 'Wisconsin'
myDictionaryOfDictionaries['Second']['City'] = 'Wausau'
myDictionaryOfDictionaries['Second']['KnownFor'] = 'Cheese'

for someKey in myDictionaryOfDictionaries:
	print "Printing keys in '{}'".format(someKey)
	for anotherKey in myDictionaryOfDictionaries[someKey]:
		print "Key is '{}' and value is '{}'".format(anotherKey, myDictionaryOfDictionaries[someKey][anotherKey])
```

## Lists in Dictionaries

You can even put lists in dictionaries; matte rof fact, let's throw in anothe rdictionary for good measure!
```
firstList = ['Captain Reginald Scary', 'Russford T. Diggins', 'Peter P. Squalls']
secondList = ['Don Bruno', 'Sammy Bruno', 'Stabby Bruno', 'Moley Bruno']

myDictionaryOfLists = {}
myDictionaryOfLists['owner'] = 'Brent'
myDictionaryOfLists['pirates'] = firstList
myDictionaryOfLists['mobsters'] = secondList

myDictionaryOfLists['colors'] = [] # its important to initialize the list
myDictionaryOfLists['colors'].append('red')
myDictionaryOfLists['colors'].append('green')
myDictionaryOfLists['colors'].append('yellow')
	
myDictionaryOfLists['supriseDictionary'] = {}
myDictionaryOfLists['supriseDictionary']['State'] = 'Pennsylvania'
myDictionaryOfLists['supriseDictionary']['City'] = 'Gilbertsville'
myDictionaryOfLists['supriseDictionary']['Known For'] = 'Cheesesteaks'
	
for key in myDictionaryOfLists:
	if (type(myDictionaryOfLists[key]).__name__ == 'list'):
		print "The key was '{}' and the second element in that list is: {}".format(key, myDictionaryOfLists[key][1])
	elif (type(myDictionaryOfLists[key]).__name__ == 'str'):
		print "The key '{}' was a simple string with contents '{}'".format(key, myDictionaryOfLists[key])
	elif (type(myDictionaryOfLists[key]).__name__ == 'dict'):
		print "The key '{}' was a dictonary from the state: {}".format(key, myDictionaryOfLists[key]['State'])
```

In the same dictionary object, we can have strings, lists, and even other dictionaries!

# Type: Finding Type of Variable

There are times when yo uare programming that you may have trouble around a specific variable: you may not know exactly what type of variable is returned from a function. In order to tell. you can use the 'type()' function as so:
```
myVar = "Me and only me")
print type(myVar)
```

The above returns a `<type 'str'>`, but it can identify any variable, object, or data type.

In order to practically use the information from `type()`, you can use `type().__name__`; for example:
```
varString = "Brent"

print "The type of variable is a {}".format(type(varString).__name__)
```

	
---

# Functions

## Basics of Functions

You can make functions in Python that can be called in other parts of the code. functions must have no whitespace before the definition, start with the `def` keyword, have a parameter list in `( ... )` and have a colon (`:`) at the end. Here is an example of a function called 'myFunction', which simply takes a string, capitalizes it, and then returns it:
```
import string

def myFunction(passedString):
	returnString = string.capitalize(passedString)
	return returnString
	
if __name__ == '__main__':
	myGreeting = "hello"
	myName = "brent"
	
	myName = myFunction(myName)
	
	print myFunction(myGreeting) + ", " + myName + "!"
	
```

The above will print "Hello, Brent!" to the screen. Note that:
* We used the library `string` to capitalize the words.
* The `return` statement sends the variable back to the code that called it.
* We used the `+` symbol to concatenate the strings in the final print command.
* We can use the function in-line (i.e. we used 'myFunction(myGreeting)' directly in the print line).

## Arguments in Functions

You can pass multiple arguments to a function; furthermore, you can have defaults for specific arguments.  Example:

```

def myCoolFunction(myName, myAge, myOccupation = "Programmer", myMainHobby = "Gaming"):
	print "My name is " + myName + ", I am " + myAge + " years old; for work I am a " + myOccupation + " and for fun I " + myMainHobby

if __name__ == '__main__':
	myCoolFunction("Brent", 38, myMainHobby = "write documentation")
```

Notes:
* The arguments that do _not_ have defaults set MUST be passed by the programmer in the general code.
* You can set defaults for arguments.
 * This allows the programmer to simply not set that argument and the default is used (in the example above, the default for 'myOccupation' was used and was thus not set in main).
 * If the user wants to set a specific variable, they can do so by name (as 'myMainHobby = "write documentation"' does above).
 
!> You *MUST* define the arguments that are required _first_ (as we do with 'myName' and 'myAge' above), and _then_ you can list the variables that have default settings. If you do not, it will error out!


## Returning Multiple Variables with Functions

Python can return several variables of different types. Here is an example:
```
import string

def myFunction(passedString, passedInteger):
	returnString = string.capitalize(passedString)
	passedInteger = passedInteger + 1
	return returnString, passedInteger
	
if __name__ == '__main__':
	myGreeting = "hello"
	myInteger = 1
	
	capitalizedGreeting, myNewInteger = myFunction(myGreeting, myInteger)
	
	print capitalizedGreeting + ", the new value for the integer is " + str(myNewInteger)
	
```

We simply listed the variables to be returned in the `return` statement separating the variables with commas (and the calling code had two variables that would accept these returned variables).

> Note the use of `str(myNewInteger)`; str() converts its arguments to a string. We had to convert myNewInteger from an integer to a string, otherwise the `+` would be interpreted as addition and not concatenation.

# Classes

You are able to make classes in Python that can be used later on in your code.  Here is an example which creates a class called 'Felicitations':
```
#!/usr/bin/anaconda/python2.7/bin/python

class Felicitations(object): 
		def __init__(self): 
				self.felicitations = "Greetings" 
		def addon(self, word, separator = ' '):
				self.felicitations = self.felicitations + separator + word 
		def printme(self): 
				print self.felicitations
	
if __name__ == '__main__':
	myName = "Brent"
	
	myNewClass = Felicitations()
	myNewClass.addon(",", '')
	myNewClass.addon(myName)
	myNewClass.printme()
```

> 'Functions' contained within classes are not technically called functions, they are referred to as 'methods'.

Notes:
* The class is defined with the word 'class', and you must pass an 'object' to it at the very least (if you don't understand why, just do it for now).
* The class defines methods (functions) like regular functions, but note there is [whitespace](learn_to_code/python?id=tabs-and-whitespaces) before the 'def' indicating it belongs to the class.
 * All methods has the `self` parameter as the first argument. This is a requirement, but notice when in the main function you do not has to pass anything to `self` (so in the main code we say 'myNewClass.addon(myName)' and not 'myNewClass.addon(self, myName)').
   * For now do not worry about the meaning behind `self`; just know that it is used by a class to reference itself; its most important when referencing variables in its own instance (like the variable 'felicitations').
* The `__init__` is the constructor
 * The constructor is a special method that is implicitly called every time a class is used to create an object (which happens in the line `myNewClass = Felicitations()`).
 * It is called exactly once; it is traditionally used to set-up the class, if that is required.
* The variable 'felicitations' is defined in the constructor and is set to "Greetings".
 * You can use variables that are specific to that instance of the class anywhere in the class itself; the only caveat is you *must* reference it using the `self` variable beforehand (in our example, to reference the class variable 'felicitations' we actually have to say 'self.felicitations')
 * The variables in the class defined using `self` persist as long as the instanced class exists.
* We must actually set a variable to the class in order to use it (we do this with the 'myNewClass' variable, and technically its not a variable but an *object*).
 * Once set, we can call the methods in the class with dot notation (we do this in the line 'myNewClass.addon(myName)', for example - we are calling the method 'addon' which we defined in the class).
 

# Passing Arguments to Python

Its possible to pass arguments (i.e. setting initial variables) to your Python script from the command line; this really opens up the possibilities of your script (in practice, arguements are almost always used).

## Basic Argument Passing

The most basic ways of getting the values of passed arguments is to use `sys.argv[]`, which is a list of the arguments passed to Python. Here is a very basic script using sys.argv:
```
import sys
	
if __name__ == '__main__':
	scriptName = sys.argv[0]
	firstVar = sys.argv[1]
	secondVar = sys.argv[2]
	
	print "The script's name is " + scriptName + ", the first variable passed was '" + str(firstVar) + "', and the second variable passed was '" + str(secondVar) + "'." 
```

I called my file 'argTest.py' and called it with the command `python argTest.py one two`, which passes two arguments (the string "one" and the string "two")

Notes:
* sys.argv is actually a list, so reference it as such.
* The first element in sys.argv is the file name, and it is always the file name.
* Arguments that are intended to be strings do not have to be wrapped in quotes, but if they contain any whitespace characters you *must* wrap it in quotes.

## Advanced Argument Passing

The module `argparse` was built to handle arguments. Here is an example of its use:

```
import argparse
import sys

if __name__ == '__main__': 

	parser = argparse.ArgumentParser(description='Process some integers.')
	parser.add_argument("-u", "--user", default="Max Power", help="Please give your username.")
	parser.add_argument("-ic", "--likesIceCream", type=int, default="1", choices=[0,1], help="Do you like ice cream? (0 or 1)")

	try:
		args = parser.parse_args()
		userName = args.user
		iceCreamAdvocate = args.likesIceCream
	except:
		parser.error("Invalid arguments.")
		sys.exit(0) 
	
	if (iceCreamAdvocate == 1):
		print "Your name is " + userName + " and you like ice cream."
	else:
		print "Your name is " + userName + " and you despise ice cream."
```

If this script is named 'testArgs.py', you can run it with `python test2.py -ic 0 -u Bob` and it will print `Your name is Bob and you despise ice cream.`

Notes:
* The first parameter in 'parser.add_argument' is the flag; in the first example that is '-u'
 * The second parameter is the name of the variable that will be set in 'parser.parse_args()' (and for simplicity, we set 'args' to 'parser.parse_args()').
 * The flag '-u' sets the 'user' variable in 'parser.parse_args()'; if the user passes '-u Bob', this means 'parser.parse_args().user' (or in our case, args.user) will be set to "Bob".
* If the parameters are not set, the default will be taken.
* You can limit the choices with the choices list as shown above.

!> If you are passing a number, it is *critical* to set the type; in the above statement the bit 'type=int' tells the compiler that the variable likesIceCream is an integer. If this is not done it will default to a string, and that will cause a huge mess downstream if you try to add to that number, multiply it, etc etc as it will be interpreted as a string!


# Running System Commands

> Most system commands use the `os` or `socket` libraries.

You can run many system commands in Python. Some of these are:
* Environment Settings
 * Get an environment variable
   * `os.environ['someEnvironmentName']`
 * Get program's process ID
   * `os.getpid()`
 * Get current process' group ID
   * `os.getgid()`
 * Get current user's ID
   * `os.getuid()`
 * Get current user's login
   * `os.getlogin()`
* Computer Networking Info
 * Get hostname:
   * `socket.gethostname()`
   * Alternative: `platform.node()`
 * Get fqdn
   * `socket.getfqdn()`
* File and Directory Manipulation
 * Rename a File
   * `os.rename('ladybug.txt', 'butterfly.txt')`
 * Delete a File
   * `os.remove('butterfly.txt')`
 * Check if File Exists
   * `retVal = os.path.isfile('/somepath/somefile.someextension')` #Returns True or False
 * Check if Directory Exists
   * `retVal = os.path.isdir('/somedir')` #Returns True or False
 * Make a new Directory
   * `os.mkdir("/path/to/new/directory")`
 * Get working directory
   * `os.getcwd()`
 * Change Working Directory
   * `os.chdir("/new/working/directory")`
 * Delete a Directory
   * `os.rmdir("/path/to/dir/to/be/deleted")`
 
	
## System Commands in Background

This is how you can run system commands in the background. Basically, you use the `subprocess` library; you pass a list to the function `subprocess.check_output()`, with each element that is separated by whitespace represented in the list (so the command `grep -r "someText" *` would be a list `['grep', '-r', '"someText"', '*']`. Note that the variable `retval` contains whatever was printed to the screen. This example saves the results of `ls -la` to the variable `retVal`:
```
retVal = subprocess.check_output(["ls", "-la"])
```

Ssometimes you need to include it all on one line (instead of making a list with each part that is separated by a space as an element, as directly above); if you wish to do this just remove the list brackets `[]` and include `shell=True`:
```
retVal = subprocess.check_output("ls -la", shell=True)
```

Note that if you dont care about the output you can just run call:
```
retVal = subprocess.call(["ls", "-la"])
```

# Working With Files

## Opening The File

Say we want to open a file - ladybug.txt - for writing:
```
myFile = open("ladybug.txt", "w")
```

The file handle is `myFile`, and we have opened it for writing (note the "w"). There are other options we can use too:
* "w" is for writing
* "wb" will write a binary file instead of a text file (necessary for image files, media files, etc)
* "r" is for reading
* "rb" is for reading a binary file
* "a" stands for 'append', which wont overwrite an existing file and will simply add to it
* "ab" is append for binary files
 
> Binary means you are actually reading and writing the bits that make up the characters, whereas not specifying binary simply saves the characters 'as you type them'. Non-binary files tend to be stuff like text files; binary files are things that need more than the spectrum of what is possible to type (things like images and media files).

## Closing a File

Its good practice to close any file you have open so as to not cause problems. To close a file:
```
myFile.close()
```

## Writing to a File

Once we open the file, we can write some lines to it with the `.write()` method:
```
myFile = open("ladybug.txt", "w")

myFile.write("new FILE, yay!\n")
myFile.write("another line\n")
myFile.write("gooooooooo ladybugs\n!")

myFile.close()
```

> Note the use of '\n' - Python does not print a newline at the end so you must use this to do so:

## Reading from a File

There are a couple of ways you can read from a file. The first is to just read the _entire_ file contents into a variable with `.read()`:
```
myFile = open("ladybug.txt", "r") #open for reading
myString = myFile.read()
myFile.close()
```

In some cases you may wish to read in characters in blocks; to do so just specify an integer in `.read()`, and that number of characters will be read in:
```
myFile = open("ladybug.txt", "r")
myString = myFile.read(10)
myFile.close()
```
The above reads in exactly 10 characters.
<br>

If you wanted to read a specific line in a file, you could specify that with the `.readline()` method:
```
myFile = open("ladybug.txt", "r")
myString = myFile.readline(3) # reads the 3rd line
myFile.close()
```

Or if you just wanted to read in all lines you could do this like so:
```
myFile = open("ladybug.txt", "r")
myListOfLines = myFile.readlines()
myFile.close()
```
* This is different than `.read()` as this returns a list, one element for each line.


Its more practical to loop over all read lines, like so:
```
myFile = open("ladybug.txt", "r")
for myLine in myFile:
	print myLine

myFile.close()
```

Finally, you can use the `with` statement and it will assume everything indented under the `with` pertains to the file.  The cool thing about `with` is yo udo not have to use the `.close()` method, as its understood to be closed when we exit `with`:
```
with open("ladybug.txt", "r") as myFile:
	data = myFile.readlines()
	
	for line in data:
		print line #prints one line
```

	#note that the line contains the newline / \n character - to get rid of that you can strip all whitespace from the 
		#line with str.strip() (strips all leading and trailing whitespace), str.lstrip() (strips all leading whitespace), or 
		#str.rstrip() (strips all trailing whitespace)
		#NOTE that you can also specify which characters to strip, so if you want to keep spaces but eliminate the newline 
		#you would use str.strip('\n')
		myNewLine = myNewLine.rstrip()
		print "The line is '{}'".format(myNewLine)

	#close the file
	myFile.close()
	
---

# XML Parsing

Here is an example 
```

import xml.etree.ElementTree as ET

#XML sample.xml Setup:
"""
<?xml version="1.0" encoding="UTF-8"?>
<employees>

	<employee>
		<fname>Brent</fname>
		<lname>Wagenseller</lname>
		<home>Pennsylvania</home>
		<expertise name="SQL"/>
		<expertise name="Python"/>
		<expertise name="R"/>
		<expertise name="Java"/>
		<expertise name="Machine Learning"/>
	</employee>
	<employee>
		<fname>Barbara</fname>
		<lname>Nevar</lname>
		<home>Florida</home>
		<expertise name="Project Management"/>
		<expertise name="CMR"/>
	</employee>

	<employee>
		<fname>Rick</fname>
		<lname>Wheeler</lname>
		<home>Wisconsin</home>
		<expertise name="Unix"/>
		<expertise name="nagios"/>
		<expertise game="football" name="cheese"> Green Bay Cheese!</expertise>
	</employee>

</employees>
"""

if __name__ == "__main__":

	tree = ET.parse("sample.xml")
	
	#get the root node
	root = tree.getroot()
	
	#simply print the tag - in root's case it will be 'employees'
	print root.tag
	
	#cycle through all employees in the root
	for employee in root:
		#cycle through all the elements in the employee element
		for elem in employee:
			#print the tag and text
			#note if the text has nothing in it (like many of the 'expertise' tags) the value
			#  will be None
			if elem.text is None:
				print "{} is empty.".format(elem.tag)
			else:
				print "{} is {}".format(elem.tag, elem.text)
			
			#the .attrib is a dictionary of all attributes in the element (the 'expertise' tag
			#     above has at least one ('name') and one of these tags has a second ('game'))
			#     all tags actually do have an attribute, so the 'for' loop below shouldnt fail; 
			#     that said it may be empty, but if its empty the for loop will simply be passed over
			for key in elem.attrib:
				print "key: {} value {}".format(key,elem.attrib[key])
				
	print "---------------------------------------------------------------------------------"
	
	#the tree can also be traversed by accessing the element's multidimensional array
	#(this prints 'Brent', as we are really accessing root[employees][employee])
	print root[0][0].text 

```


# Exception Handling

> For more info visit [tutorialspoint](https://www.tutorialspoint.com/python/python_exceptions.htm).

You can use the 'assert' function to assert what a variable should be; if the variable configuration forces a 'False', an exception will be raised. HOWEVER, if the assertion is False _after_ the 'assert', an exception will _not_ be raised:

```
if __name__ == '__main__':
	CurrentYear = 2019
	BirthYear = 1980
	Age = 39
	
	#if this evaluated to false this would raise an exception - but it evaluates to true so no exception will be raised
	assert ((CurrentYear - BirthYear) >= Age), "Impossible Age"
	
	print "Yoooo"
	
	#This WOULD invalidate the above assertion, but since we are already past the assertion no exception is raised
	BirthYear = 1990 #should cause an error
	
	print "you should not get here, but you will get here"
	
	#however, if we call the 'assert' again an exception _will_ be raised
	assert ((CurrentYear - BirthYear) >= Age), "Impossible Age"
	
	print "you will absolutely not get here"
```


This shows you can catch many exceptions:
```
if __name__ == '__main__':
	
	try:
		
		fh = open("thisfiledoesntexist.txt", "r")
		CurrentYear = 2019
		BirthYear = 1990
		Age = 39
		
		print "You will get here"
		assert ((CurrentYear - BirthYear) >= Age), "Impossible Age"
		
		print "You will NOT get here as the above assertion fails"
	except AssertionError, myArgument:
		print "Exception handling right now - the above assertion failed, so this will run because there was an AssertionError"
		print "Usually you can capture the argument as well: we do this above with 'myArgument', whose value we set to: '{}'".format(myArgument)
	except IOError, myArgument:
		print "If there was an IOError it would print here (but this will not trigger as there is no IOError above)"
		print "Usually you can capture the argument as well: we do this above with 'myArgument', whose value we set to: '{}'".format(myArgument)
	except:
		#All other exceptions will be caught here
		print "Exception handling for everything else; this would print if we did not have the 'AssertionError' above."
		
	else:
		print "Anything that is here will be run if there are no exceptions; there was an assertion failure so you will never see this"
	
	print "Wrapping up - this will always print"
```
	

Other methods of try....except have specific error codes; MySQL is an example of this

```
import mysql.connector
from mysql.connector import pooling


if __name__ == '__main__':
	
	config = {
		'user': 'someUserName',
		'password': 'somePassword',
		'host': 'someFakeIP',
		'database': 'someSchema',
		'raise_on_warnings': False
	}

	cnx = None

	try:
		cnx = mysql.connector.connect(**config)
					
	except mysql.connector.Error as err:
		if err.errno == mysql.connector.errorcode.ER_ACCESS_DENIED_ERROR:
			print "Something is wrong with your user name or password"
		elif err.errno == mysql.connector.errorcode.ER_BAD_DB_ERROR:
			print "Database does not exist"
		else:
			print err
	else:
		connSuccessful = 1	
	"""
```

# kwargs (Double Asterisks)

> [Here](https://stackoverflow.com/questions/36901/what-does-double-star-asterisk-and-star-asterisk-do-for-parameters) is a beginners guide to double asterisks.

The double asterisk (or kwargs) makes a dictionary out of all variables sent to the function. You can make up variable names on the fly if you wish.

The example below sends arbitrary variables to the foo() function, and as you can see, the foo() function treats \*\*kwargs as a dictionary, where the variable names are the keys.

Example:
```
def foo(**kwargs):
	for a in kwargs:
		print "{} is the variable name, and '{}' is the value".format(a, kwargs[a])

if __name__ == "__main__":
	
	foo(mandi="mom", brent="dad", jackson="son", natalia="daughter", fatherAge = 39)
```
