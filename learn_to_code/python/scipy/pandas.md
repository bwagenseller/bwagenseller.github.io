# Pandas

# Before We Begin

It should be noted that an excellent quick resource lives at [pydata.org](pandas.pydata.org/pandas-docs/stable/10min.html).

# Main Modules

This is the module that we use for pandas:
```
import pandas as pd
```
* Note the re-name as 'pd'; this is a common convention.

Other minor imports are:
```
import difflib
import functools 
import collections
```

# DataFrames


DataFrames are the base object of pandas; they are kind of like spreadsheets (or a table in a relational database, if you prefer), so if yo uhave trouble conceptualizing a DataFrame just think of an Excel spreadsheet tab (or relational database table). 

## Bulding a Basic DataFrame

In practice, a DataFrame seems to be a [dictionary](learn_to_code/python/python_basic?id=dictionaries) of [lists](learn_to_code/python/python_basic?id=lists). The dictionary keys are _columns_, and each list represents the data in that column. All list lengths should be the same; if one list is longer or smaller than the others, Python will error out.

> It should also be noted that the _rows_ represent one entry in the 'spreadsheet' or 'relational database table'; the columns are _features_ or a collection of data that represents the same idea (like 'city', 'state', 'weight', 'height', etc - a column is centered around a concept). A _row_, on the other hand, spans all columns and represents one identifiable element or object). It is important to note that since the columns are made up of lists - and since we don't directly import rows as we do lists, at least initially under most circumstances - its important that position `n` in one list represents the same row in position `n` in all other columns. 

An example of building a DataFrame:

```
myFirstDF = pd.DataFrame({'col_A' : [17, 36, 78, 9, 3],
							'col_B' : ['one', 'two', 'three', 'four', 'five'],
							'col_C' : np.random.randn(5),
							'col_D' : np.random.randn(5)})	
```
* The function `np.random.randn(5)` is a numpy function that simply returns a list of `n` real numbers (in our case 5)

If we wanted to issue a `print myFirstDF`, we would get the following:
```
   col_A  col_B     col_C     col_D
0     17    one  0.242720 -0.186517
1     36    two -1.783160 -1.725678
2     78  three  0.051942  0.873767
3      9   four  0.549844 -0.212834
4      3   five  0.444737  0.393661
```

We see the columns, but we also see 0...4, which we did not enter; this is the index. Each DataFrame must have an index, and if you do not specify one when you create it, a default one will be created.

## Building Empty DataFrame

Its possible to build an empty dataframe, so long as you create the indexes of the dataframe; the example below shows how to do that with the use of the `index=` parameter (it also uses the [Pandas date range function](learn_to_code/python/scipy/pandas?id=date-range)):
```
dates = pd.date_range(dt.datetime(2019,02,01), dt.datetime(2019,02,28))
df = pd.DataFrame(index=dates)
```
* The above builds an empty DataFrame; that said, the indexes _are_ defined, and they are set to our date range.

## Loading from CSV

> This can be used to pull in [machine learning datasets from the web](learn_to_code/python/scipy/machine_learning_in_python?id=test-datasets); just replace the file name with the web address.

It is possible to load a dataframe from a CSV, using the method `newDataFrame = pandas.read_csv('Path_And_Name_To_CSV')`.

## Accessing Specific Columns

<font color="red" size="36"> THIS SECTION IS NOT FINISHED</font>

You can access columns in a few ways:
* Pass the column names you wish to select as a list

## Copying DataFrames

<font color="red" size="36"> THIS SECTION IS NOT FINISHED</font>

You will often have to copy an entire - or part - of a DataFrame to another dataframe. 

## Manipulating Column Names

<font color="red" size="36"> THIS SECTION IS NOT FINISHED</font>

You will frequently need to interact with the DataFrame on the column level: you will need to create new columns, rename columns, or delete (or re-order) columns.

**<font size="4">Add Columns</font>**

> We will use the [Basic DataFrame example](learn_to_code/python/python_scipy?id=bulding-a-basic-dataframe) as a base for this section.

## Row / Column Counts

To get the row or column count, you will have to use the shape[] function. For example, if you have a dataframe myDF and want to get the row count you could say:
```
rowCount = myDF.shape[0]
```

`shape[0]` means row count.

Similarly, if you wanted to get the column count you could use `shape[1]`:
```
columnCount = myDF.shape[1]
```


---

# Indexes In Dataframes

## Sorting Indexes

There is no guarantee that the indexes in the dataframe will be in order (particularly if they are dates and you imported them). In order to make sure they are in order, you can use the `sort_index` method of a dataframe object. For example, if you had a dataframe called `myDF` and you wanted to make sure the indexes were sorted, you could do the following:
```
myDF.sort_index(inplace=True)
```

Note that the above is done in-place, so no assignment required.

## Min / Max Index Values

To get the min (or max) value of the indexes, use index.min() (or max); for example, if you have a dataframe `myDF` and you want to get the min, you can get that by:
```
minIndex = myDF.index.min()
```

> You can also use max() here instead.

# DateTime in Pandas

You will have to interact with datetimes in pandas. Here are a few quick notes on the matter.

## Date Range

To get a quick date range, you can use the method `pandas.date_range`; for example, if we wanted to store the range from 2019-02-01 to 2019-02-28, we can simply run the following:
```
dates = pandas.date_range(dt.datetime(2019,02,01), dt.datetime(2019,02,28))
```
* Note that the accepted dates are `datetime` objects and NOT strings.

This function returns the datetimes in the `pandas.core.indexes.datetimes.DatetimeIndex` format. An easy way to convert this to a list of strings is by using [list comprehension](learn_to_code/python/python_basic?id=list-comprehension):
```
dates = pandas.date_range(dt.datetime(2019,02,01), dt.datetime(2019,02,28))
listOfStringDates = [d.strftime('%Y-%m-%d') for d in dates]
```

# Series

A Pandas series seems to be like a DataFrame, but it is only a single column without a column name. It should also be noted that a series does have an index.

Usually, when a single column is stripped out of a dataframe - usually with DataFrame.ix\[:,n\] - Pandas automatically converts what is returned to a Series.

## Unique Values In Series 

To find all of the unique values in a Series, we can use the `unique()` function; then, we can turn it into a [list](learn_to_code/python/python_basic?id=lists) like so:
```
myDF = pd.DataFrame({'col_A' : ['a', 'b', 'a', 'b', 'a', 'a'],
						'col_B' : ['one', 'two', 'two', 'one', 'three', 'three']})

mySlice = myDF.ix[:,0].unique().tolist()
```
* `mySlice` is a list that holds the unique values in `col_A`; in other words, `['a', 'b']`.

!> Its critical to remember this can _only_ be used on a Series and **not** a DataFrame! since we are only getting one column from the DataFrame above, Pandas automatically converts it to a Series, so using `unique()` becomes feasible.

---

# Converting to Numpy Array

Its possible to convert a pandas dataframe to a numpy array - to do so, just use the `.values` property. Here is an example that converts a pandas dataframe to a numpy array:
```
df = pd.read_csv('https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data', header=None)
	
newNumpyArray = df.iloc[0:100, 4].values
```

---


