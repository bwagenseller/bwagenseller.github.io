# Numpy

# Before We Begin

It should be noted that an excellent quick resource lives at [pydata.org](pandas.pydata.org/pandas-docs/stable/10min.html).

# Main Modules

This is the module that we use for numpy:
```
import numpy as np
```
* Note the re-name as 'np'; this is a common convention.

Other minor imports are:
```
import difflib
import functools 
import collections
```

# Defining Numpy Objects

## Basic Numpy Array Creation

a basic Numpy array takes a _list of lists_ as base. This means that in order to create a basic numpy array, you must start with a [list](learn_to_code/python/python_basic?id=lists) of lists like so:
```
[['a' 'b' 'c'], 
 ['d' 'e' 'f'], 
 ['g' 'h' 'i']]
```
* This is a list that contains 3 lists.

Here is an example of how we can make a basic NumPy array with a list of lists:
```
initialListOfLists = [[1, 2, 3], ['a', 'b', 'c'], ['d', 'e', 'f'], ['g', 'h', 'i']]
newNumpyArray = np.array(initialListOfLists)
print newNumpyArray
```

## NumPy Array Filled With Zeros

Here is how you can fill a NumPy array and initialize to all 0's:
```
numRows = 10
numColumns = 20
newNumpy = numpy.zeros([numRows, numColumns], dtype = int)
```
* dtype can be 'int', 'float', or you can leave this parameter off entirely.
* The brackets are critical!

## NumPy Array Filled With Ones

Here is how you can fill a NumPy array and initialize to all 1's:
```
numRows = 10
numColumns = 20
newNumpy = numpy.ones([numRows, numColumns], dtype = int)
```
* dtype can be 'int', 'float', or you can leave this parameter off entirely.
* The brackets are critical!


## Empty NumPy Array

Its also possible to create an 'empty' NumPy array where its simply not initialized; this means that random values may exist in the array when its created, but its slightly faster than filling with ones or zeros initially as that action is skipped.

Here is how you can create an empty NumPy array (with unknown defaults):
```
numRows = 10
numColumns = 20
newNumpy = np.empty[numRows, numColumns], dtype = int)
 = numpy.ones()
```
* dtype can be 'int', 'float', or you can leave this parameter off entirely.
* The brackets are critical!


## Other Initialization Methods

Another way to quickly create a NumPy array and fill it with a specific value is with the fill() method.

For example, if we wanted to fill a new NumPy array with `nan` (not a number), we could do so like this:
``
newNumpy = np.empty[numRows, numColumns], dtype = int)
newNumpy.fill(np.nan)
print newNumpy
``

This creates the NumPy array and then fills the entire thing with [numpy.nan](learn_to_code/python/scipy/numpy?id=representing-null-in-cells).

> `newNumpy[:] = np.nan` could also work instead of `newNumpy.fill(np.nan)`, but the .fill() method is slightly faster in terms of operation.

# Representing NULL in Cells

<font color="blue">None</font> / NULL - i.e. missing data - is a common occurrence for any dataset. While its possible to use <font color="blue">None</font> in NumPy arrays (as well as Pandas), you should never use it: use `numpy.nan` ('not a number') instead. If you use <font color="blue">None</font>, it forces an `object` type in NumPy / Pandas and thus kills all possible vectorization. numpy.nan, on the other hand, counts as a float value (a special float value at that). This means that if we wish to use vectorization on a NumPy column with some elements being numpy.nan we can, whereas if some elements were <font color="blue">None</font> we could not.

In addition, two Pandas functions - pandas.isnull() and pandas.notnull() - exclusively use numpy.nan for NULL. These functions are usually critical, so please, use numpy.nan for NULL data in NumPy / Pandas and not <font color="blue">None</font>.

> In short: always use numpy.nan to represent missing data; never use <font color="blue">None</font>.



# Referencing Rows and Columns (Numpy)

## General Referencing

Referencing specific rows and columns in numpy is very similar to Pandas; the following assumes a numpy array `numpyArray` has already been created:
```
numpyArray[rowStart:rowEnd, columnStart:columnEnd]
```

Please note that 
* rowStart / rowEnd / columnStart / columnEnd are integers.
* rowStart and columnStart start at 0.
* rowEnd and columnEnd are exclusive; so the index will include up to - but NOT including - that number.
* Its possible to include _all_ rows, _all_ columns, or both by simply using a `:` in its place like so: `numpyArray[:, :]`
 * This means 'return all rows and all columns'; obviously you can restrict it.

## Referencing Individual Rows or Columns

Its also possible to reference _individual_ rows or columns if you just use the _one_ number.

Example of referencing a single row: `numpyArray[3, :]`
* This references row 3, all columns.

Example of referencing a single column: `numpyArray[:, 6]`
* This references all rows, column 6.

Example of referencing a single cell: `numpyArray[3, 6]`
* References the _specific single value_ in row 3, column 6.

## Referencing From the Back

You can also reference from the opposite direction using negative values.

Example of referencing the last column (all rows) using -1: `numpyArray[:, -1]`

Example of referencing the 4th to last column (all rows) using -1: `numpyArray[:, -4]`

## Referencing Lists of Unordered Rows or Columns

Finally, you can reference unordered rows and columns _so long as they are in_ a [list](learn_to_code/python/python_basic?id=lists).

Example of referencing all rows and precisely columns 3, 5, 8: `numpyArray[:, [3, 5, 8]]`

## Setting NumPy Arrays

[Any of the methods for referencing rows and columns](learn_to_code/python/scipy/numpy?id=referencing-rows-and-columns-numpy) mentioned can be used to assign values to a NumPy array. The only caveat is the dimensions (usually rows and columns) **MUST** match between what you are setting and the source of the data. The one exception to this is when you are setting an entire chunk of data to one scalar value; so for example, if you wanted to set the 4th (3rd with a 0 index) column in a NumPy array to 5, you could do it like so:
```
someNumPyArray[:, 3] = 5
```

If you want to set the _entire_ NumPy array to a scalar there are a few options; firstly, if you want to set the entire array to [ones](learn_to_code/python/scipy/numpy?id=numpy-array-filled-with-ones) or [zeros](learn_to_code/python/scipy/numpy?id=numpy-array-filled-with-zeros), there are methods that can do that. If yo uwant to set it to anything else though, there are two options: `[:]` and the `.fill()` method.

If you have a NumPy array `someNumPyArray` and you wanted to initialize all values to 5, you could simply write `someNumPyArray[:] = 5` (note: `someNumPyArray = 5` does NOT work as it re-casts the variable). You can also use `someNumPyArray.fill(5)` instead; `.fill()` is preferred as its faster.

> A popular variable to initialize to is [numpy.nan](learn_to_code/python/scipy/numpy?id=representing-null-in-cells), which is a version of <font color="blue">None</font> or NULL.

# WHERE Statement

Numpy has a WHERE statement, in which anything in the array that matches that criteria is altered, as well as anything that does not match.  This is a bit confusing, but basically you have the option to change BOTH sets of items - those that match and thsoe that do not.

The format is `np.where('some condition', 'change to this if true', 'change to this if false')`.

Here is an example:
```
a = np.array([[0, 1, 2],
			[0, 2, 4],
			[0, 3, 6]])

print np.where(a < 4, a, -1)  # -1 is broadcast
```
The result is:
> [[ 0,  1,  2],  
> [ 0,  2, -1],  
> [ 0,  3, -1]])
	

# SQL-Like Queries

Its possible to 'query' (in a sense) a numpy array; for example:
```
initialListOfLists = [[1, 2, 3], [40, 50, 60], [7, 8, 9], [10, 11, 12], 
					[130, 140, 150], [16, 17, 18], [190, 200, 210]]
newNumpyArray = np.array(initialListOfLists)
revisedNumpyArray = newNumpyArray[(newNumpyArray[:, 2]>=60) & (newNumpyArray[:, 2]<180)]
```

Here is what is saved to revisedNumpyArray:
```
[[ 40  50  60]
 [130 140 150]]
```

Here we created a numpy array and restricted it to only the lists where position 2 was greater than/equal to 60 *AND* less than 180. What is returned is all rows that match this criteria (see what is returned directly above).

How this works is both `(newNumpyArray[:, 2]>=60)` and `(newNumpyArray[:, 2]<180)` return a boolean (<font color="blue">True</font> or <font color="blue">False</font>) for _each_ row in `newNumpyArray`; both booleans are then joined with an AND and re-evaluated. The rows that equate to <font color="blue">True</font> are returned, and the rows that equate to <font color="blue">False</font> are ignored.

!> For this to work, the row counts in the numpy array being queried **MUST** match the rowcounts that are returned by the boolean statements! Its not true that the boolean statements must utilize the numpy array being evaluated per se, but the row counts MUST align!

# Row and Column Sizes

Here is how you can get the row count from a numpy object: `print numpyObect.shape[0]`

Here is how you can get the column count from a numpy object: `print numpyObect.shape[1]`

# Random Row Permutation

Often times, it will be required to randomize the rows in a dataset (for example, the data must be randomized after every [epoch](learn_to_code/machine_learning/ml_key_terms?id=epoch) when using [stochastic gradient descent](learn_to_code/machine_learning/ml_key_terms?id=stochastic-gradient-descent)). Its possible to easily get a randomized list of integers that will correspond to the number of rows in the numpy array.

To do this, we can use `numpy.random.permutation(X)`, which will return a `numpy.ndarray` of numbers from 0 to X in a random order.

From here, simply set the numpy array (or a new array) equal to the old array with the created random `numpy.ndarray` as its reference; this will shuffle the rows in the numpy array!

Example:
```
newNumpyArray = np.array([[0, 1, 2, 3, 4, 5], [10, 11, 12, 13, 14, 15], [20, 21, 22, 23, 24, 25], [30, 31, 32, 33, 34, 35], [40, 41, 42, 43, 44, 45], [50, 51, 52, 53, 54, 55]])
	
r = numpy.random.permutation(len(newNumpyArray))

newRandomizedArray = newNumpyArray[r]

print r
print type(r)
print newRandomizedArray
```

This returns:
```
[2 4 0 3 5 1]
<type 'numpy.ndarray'>
[[20 21 22 23 24 25]
 [40 41 42 43 44 45]
 [ 0  1  2  3  4  5]
 [30 31 32 33 34 35]
 [50 51 52 53 54 55]
 [10 11 12 13 14 15]]
```

# Finding Unique Entries (Numpy)

You can find unique entries in a numpy array with np.unique; this method will eliminate all duplicates; so if the array was [1, 1, 1, 2, 3] the output of np.unique would be [1, 2, 3]. Example:
```
print np.unique(numpyArray[:, -1])
```
* This prints a [list](learn_to_code/python/python_basic?id=lists) of all unique entries in the last column of `numpyArray`.

# Concatenation

!> NumPy arrays are stored in contiguous blocks of memory, so concatenating them means an entirely new array must be created. It is often said that if you know the end row / column count ahead of time, you [create the NumPy array first](learn_to_code/python/scipy/numpy?id=defining-numpy-objects), then simply populate it after the fact.

We can concatenate multiple numpy arrays using the vstack() method, which vertically concatenates numpy arrays. Here is an example:

```
	initialListOfLists = [[1, 2, 3], [40, 50, 60], [7, 8, 9], [10, 11, 12], 
						[130, 140, 150], [16, 17, 18], [190, 200, 210]]
	middleNumpy = np.array(initialListOfLists)

	first = np.array([['a', 'b', 'c']])
	last = np.array([['d', 'e', 'f']])

	newNumpyArray = np.vstack( (first, middleNumpy, last) )
	print newNumpyArray
```

This vertically concatenates the three numpy arrays: `first` is added first, then `middleNumpy`, then `last`. Its critical that all concatenated numpy arrays have the same column count. The results from the above concatentation using vstack() are:

```
[['a' 'b' 'c']
 ['1' '2' '3']
 ['40' '50' '60']
 ['7' '8' '9']
 ['10' '11' '12']
 ['130' '140' '150']
 ['16' '17' '18']
 ['190' '200' '210']
 ['d' 'e' 'f']]
```

> Note that the numerical values in `middleNumpy` were converted to characters; this happens when you mix numbers and characters, so be careful!

# Numerical Evaluation in NumPy

## Correlation

Finding the [correlation](learn_to_code/machine_learning/evaluating_models?id=correlation) between two numbers is relatively easy in NumPy: simply gather two data sets that you want to check their correlation, put them both into two separate NumPy arrays, and compare like so:

```
c = numpy.corrcoef(numpyArrayOne, y=numpyArrayTwo)
```

`c` will hold the correlation coefficient.

## RMSE

The RMSE (root mean square error) is not that difficult to find using NumPy; however, there is no method for the RMSE. It can be found like so: simply gather two data sets that you want to check the RMSE between, put them both into two separate NumPy arrays, and run:
```
rmse = math.sqrt(((numpyArrayTwo - numpyArrayOne) ** 2).sum()/numpyArrayTwo.shape[0])
```

# Mathematics In Numpy

> Note that all [methods of cell referencing](learn_to_code/python/scipy/numpy?id=referencing-rows-and-columns-numpy) can be used in all mathematical operations!

## Mean

> Note that all [methods of cell referencing](learn_to_code/python/scipy/numpy?id=referencing-rows-and-columns-numpy) can be used when finding the mean!

If we want to find the mean of values in numpy, we can use the [mean()](https://docs.scipy.org/doc/numpy-1.13.0/reference/generated/numpy.mean.html) function. If we want to get the mean of all numbers do not include a parameter; if we want the mean of the rows, use the parameter `axis=1`, and if we want the mean of the columns, use the parameter `axis=0`.

Example:
```
newNumpyArray = np.array([[1, 5, 9], [3, 7, 11]])

AverageOfAllValues = newNumpyArray.mean()
AverageOfTheRow = newNumpyArray.mean(axis=1)
AverageOfTheColumn = newNumpyArray.mean(axis=0)

print AverageOfAllValues
print AverageOfTheRow
print AverageOfTheColumn
```

This will print:
```
6.0
[5. 7.]
[ 2.  6. 10.]
```


## Standard Deviation

> Note that all [methods of cell referencing](learn_to_code/python/scipy/numpy?id=referencing-rows-and-columns-numpy) can be used when finding the standard deviation!

If we want to find the standard deviation of values in numpy, we can use the [std()](https://docs.scipy.org/doc/numpy/reference/generated/numpy.std.html) function. If we want to get the standard deviation of all numbers do not include a parameter; if we want the standard deviation of the rows, use the parameter `axis=1`, and if we want the standard deviation of the columns, use the parameter `axis=0`.

Example:
```
newNumpyArray = np.array([[1, 5, 9], [3, 7, 11]])

STD_OfAllValues = newNumpyArray.std()
STD_OfTheRow = newNumpyArray.std(axis=1)
STD_OfTheColumn = newNumpyArray.std(axis=0)
	
print STD_OfAllValues
print STD_OfTheRow
print STD_OfTheColumn
```

This will print:
```
3.415650255319866
[3.26598632 3.26598632]
[1. 1. 1.]
```

## Natural Log

To quickly take the natural log of all numbers, use `np.log()`. Example:
```
newNumpyArray = np.array([[10, 20, 30], [1000, 2000, 3000]])
loggedArray = np.log(newNumpyArray)
print loggedArray
```

This will print:
```
[[2.30258509 2.99573227 3.40119738]
 [6.90775528 7.60090246 8.00636757]]
```

## Log Base 10

!> You can also use base 2, just replace `10` with `2`.

To quickly take the log of all numbers (base 10), use `np.log10()`. Example:
```
newNumpyArray = np.array([[10, 20, 30], [1000, 2000, 3000]])
loggedArray = np.log10(newNumpyArray)
print loggedArray
```

This will print:
```
[[1.         1.30103    1.47712125]
 [3.         3.30103    3.47712125]]
```



# Printing Options

There are a few settings you can tweak that will change how NumPy displays printed arrays.

## Turn Off Scientific Notation

In order to turn off scientific notation, run:
```
np.set_printoptions(suppress=True)
```

## Print Entire NumPy Array

By default, printing a NumPy array will truncate portions of itself when printed. To stop this from happening, run:
```
np.set_printoptions(threshold=np.nan)
```

## Print NumPy Array Row In A Single Line

By default, printing a NumPy array will 'wrap' a row if its too long; in order to avoid a printed NumPy array row to appear on multiple lines, run:
```
np.set_printoptions(linewidth=np.inf)
```
	
	