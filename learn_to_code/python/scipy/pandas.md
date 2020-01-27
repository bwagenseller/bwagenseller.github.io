# Pandas
<!-- clear for public use. -->

!> Please note, this section is not finished - it is partially finished, but it trails off as a collection of my scripts. I will be going through this and organizing it nicely later.

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


DataFrames are the base object of pandas; they are kind of like spreadsheets (or a table in a relational database, if you prefer), so if you have trouble conceptualizing a DataFrame just think of an Excel spreadsheet tab (or relational database table). 

## Basics of a DataFrame

Before we even build a dataframe, you should know that you can print a dataframe `someDataFrame` as you would anything else:  
```
print someDataFrame
```  

This will print the dataframe `someDataFrame`.  Its helpful to know this, as often you have to print out the dataframe to see how your experiment went.  Here is an example:
```
   col_A  col_B col_C     col_D
0     17    one    aa  1.586336
1     36    two    aa -0.679073
2     78  three    aa -0.517077
3      9   four    bb -1.068864
4      3   five    bb  0.079854
5     47    six    aa -2.177551
```  
* The first column is the [index](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes); you do not have to know what this is now, but just know its a way of identifying a row.  
 * Be default, the index is an auto-incremental integer.  
 * This index has no associated name, but they _can_ have a name.  
* All of the other columns are data columns - columns `col_A`, `col_B`, `col_C`, and `col_D`. These can be named anything.  
 * These columns are known as [features or factors](learn_to_code/machine_learning/ml_key_terms?id=features) in machine learning.  
* Each row represents one [sample](learn_to_code/machine_learning/ml_key_terms?id=sample) of the data - that is to say, one example of an event that has one data point per column / [feature](learn_to_code/machine_learning/ml_key_terms?id=features).

As mentioned, the previous dataFrame example did not have a named index - here is an example of one that does:  
```
         col_A  col_B col_C     col_D
myIndex                              
0           17    one    aa  0.416543
1           36    two    aa  0.855646
2           78  three    aa -1.323890
3            9   four    bb -0.897986
4            3   five    bb -0.433660
5           47    six    aa  0.502001
```  
* As you can see, `myIndex` is the index name in this example. Notice its much closer to its data, which is an easy way to visually see indexes in a printed dataFrame.

## Bulding a Basic DataFrame

In practice, a DataFrame seems to be a [dictionary](learn_to_code/python/python_basic?id=dictionaries) of [lists](learn_to_code/python/python_basic?id=lists). The dictionary keys are _columns_, and each list represents the data in that column. All list lengths should be the same; if one list is longer or smaller than the others, Python will error out.

> It should also be noted that the _rows_ represent one entry in the 'spreadsheet' or 'relational database table'; the columns are _features_ or a collection of data that represents the same idea (like 'city', 'state', 'weight', 'height', etc - a column is centered around a concept). A _row_, on the other hand, spans all columns and represents one identifiable element or object). It is important to note that since the columns are made up of lists - and since we don't directly import rows as we do lists, at least initially under most circumstances - its important that position `n` in one list represents the same row in position `n` in all other columns. 

An example of building a DataFrame:

```
myFirstDF = pd.DataFrame({'col_A' : [17, 36, 78, 9, 3, 47],
							'col_B' : ['one', 'two', 'three', 'four', 'five', 'six'],
							'col_C' : ['aa', 'aa', 'aa', 'bb', 'bb', 'aa'],
							'col_D' : np.random.randn(6)})	
```
* The function `np.random.randn(5)` is a numpy function that simply returns a list of `n` real numbers (in our case 5)

As stated, technically this is using a [dictionary](learn_to_code/python/python_basic?id=dictionaries) to build the dataframe. This could have been done like this with the same result:  
```
dict = {}
dict['col_B'] = ['one', 'two', 'three', 'four', 'five', 'six']
dict['col_D'] = np.random.randn(6)
dict['col_C'] = ['aa', 'aa', 'aa', 'bb', 'bb', 'aa']
dict['col_A'] = [17, 36, 78, 9, 3, 47]
mySecondDF = pd.DataFrame(dict)	
```  

If we wanted to issue a `print myFirstDF`, we would get the following:
```
   col_A  col_B col_C     col_D
0     17    one    aa -0.245120
1     36    two    aa -0.402277
2     78  three    aa -0.800608
3      9   four    bb  0.091535
4      3   five    bb  0.182003
5     47    six    aa -0.663852
```

We see the columns, but we also see 0...5, which we did not enter; this is the index. Each DataFrame must have an index, and if you do not specify one when you create it, a default one will be created.  

If we wanted to attach an [index](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes) we can do that too: 
```
	myFirstDF = pd.DataFrame({'col_B' : ['one', 'two', 'three', 'four', 'five', 'six'],
	                            'col_C' : ['aa', 'aa', 'aa', 'bb', 'bb', 'aa'],
	                            'col_D' : np.random.randn(6)}, 
	                            index = [17, 36, 78, 9, 3, 47])			
	myFirstDF.index.name = 'col_A'
```  
* Unfortunately, you have to name the [index](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes) outside of the initial line.  
* You can use multiple [indicies](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes) (an example would be `index = [[17, 36, 78, 9, 3, 47], ['Brent', 'Rick', 'Robert', 'Barb', 'Chris', 'Tracy']]`), but you would [have to set the index names differently](learn_to_code/python/scipy/pandas?id=setting-index-names).  

You can also do the above with a pre-populated dictionary:
```
myDict = {'col_A' : [17, 36, 78, 9, 3, 47],
							'col_B' : ['one', 'two', 'three', 'four', 'five', 'six'],
                            'col_C' : ['aa', 'aa', 'aa', 'bb', 'bb', 'aa'],
                            'col_D' : np.random.randn(6)}
myFirstDF = pd.DataFrame({k: v for k, v in myDict.items() if not k in ['col_A']}, 
                            index = myDict['col_A'])			
myFirstDF.index.name = 'col_A'
```  
* Unfortunately, you have to name the [index](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes) outside of the initial line.  
* The `for` loop used is the only way to prevent `col_A` from being an initial column - that said, if you do not care about this, you could have just used this instead:  
```
myFirstDF = pd.DataFrame(myDict, index = myDict['col_A'])
```  
* You can use multiple indicies (an example would be `index = [[17, 36, 78, 9, 3, 47], ['Brent', 'Rick', 'Robert', 'Barb', 'Chris', 'Tracy']]`), but you would [have to set the index names differently](learn_to_code/python/scipy/pandas?id=setting-index-names).  

!> Note that when building the dataframe with a dictionary, the columns are ordered in _alaphabetical_ order _before_ its built - so do _not_ depend on the given order, assume alphabetical order of columns when using a dictionary to build it! That said, if a column is added later, that is _not_ placed in alphabetical order and is just tacked onto the end. 

## Building Empty DataFrame

Its possible to build an empty dataframe, so long as you create the indexes of the dataframe; the example below shows how to do that with the use of the `index=` parameter (it also uses the [Pandas date range function](learn_to_code/python/scipy/pandas?id=date-range)):
```
dates = pd.date_range(dt.datetime(2019,02,01), dt.datetime(2019,02,28))
df = pd.DataFrame(index=dates)
```
* The above builds an empty DataFrame; that said, the indexes _are_ defined, and they are set to our date range.

## Build a DataFrame with a List

Its possible to build a dataframe with an existing list (and column headers). Here is an example on how to do so:

```
	myList = [[1, 2, 'c'], [4, 5, 'f'], [7, 8, 'i']]
	myColumns = ['first', 'second', 'letters']
	builtDF = pd.DataFrame(myList, columns=myColumns)
	
	print builtDF
```

This would print:
```
   first  second letters
0      1       2       c
1      4       5       f
2      7       8       i
```

!> Unlike [the dictionary example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe), the order of the columns are lept as-is and _not_ re-ordered alphabetically.  

## Loading from CSV

> This can be used to pull in [machine learning datasets from the web](learn_to_code/python/scipy/machine_learning_in_python?id=test-datasets); just replace the file name with the web address.

For the sake of argument, lets say we had a CSV `test.csv` as follows:
```
id,name, age, occupation, state, dateHired
5689, Brent, 35, Engineer, Pennsylvania, 2004-04-25
8998, Rick, , Engineer, Wisconsin, "January 30th, 1992"
3456, Barb, N/A, , Florida, 17-OCT-83
```  

It is possible to load this CSV to a dataframe using the method:
```
newDataFrame = pandas.read_csv('test.csv')
```  

**<font size="4">Parameter: skipinitialspace</font>**

`skipinitialspace` skips the initial spaces for each cell in the CSV (if spaces exist, as they do in our example). Spaces can be nefarious in two ways:
* If there is a space and nothing else, the space is recorded and _not_ `numpy.NaN` (i.e. null).
* If there is a space in front of a literal `N/A` (as in our example), this is recorded as ` N/A` and _not_ `numpy.NaN` (i.e. null).
 * Usually without spaces, `N/A` is recorded as `numpy.NaN`, but the space prevents this. 
 
To combat this, use `skipinitialspace=True` (default is `False`).  Example:
```
myCSV = pd.read_csv('test.csv', skipinitialspace=True)
print myCSV
```

prints:
```
     id   name   age occupation         state           dateHired
0  5689  Brent  35.0   Engineer  Pennsylvania          2004-04-25
1  8998   Rick   NaN   Engineer     Wisconsin  January 30th, 1992
2  3456   Barb   NaN        NaN       Florida           17-OCT-83
```  

**<font size="4">Parameter: index_col</font>**

If you wish to set the index to something in the CSV, use `index_col=`; for example:
```
myCSV = pd.read_csv('test.csv', skipinitialspace=True, index_col=['id', 'name'])
print myCSV
```

prints:
```
             age occupation         state           dateHired
id   name                                                    
5689 Brent  35.0   Engineer  Pennsylvania          2004-04-25
8998 Rick    NaN   Engineer     Wisconsin  January 30th, 1992
3456 Barb    NaN        NaN       Florida           17-OCT-83
```  
* It's clear that both `id` and `name` are now a part of the index.  

**<font size="4">Parameter: parse_dates</font>**

If you wish Pandas to store any datetime it finds as its own datetime class, use `parse_dates=True` as so:
```
myCSV = pd.read_csv('test.csv', skipinitialspace=True, parse_dates=True)
```  

prints:
```
     id   name   age occupation         state           dateHired
0  5689  Brent  35.0   Engineer  Pennsylvania          2004-04-25
1  8998   Rick   NaN   Engineer     Wisconsin  January 30th, 1992
2  3456   Barb   NaN        NaN       Florida           17-OCT-83
```  

Usually this works, but since the dates were all in different formats, this may have thrown off the auto-detection of dates.  We can _force_ columns as dates if we simply name the columns instead of setting equal to `True`:
```
myCSV = pd.read_csv('test.csv', skipinitialspace=True, parse_dates=['dateHired'])
```  

prints:
```
     id   name   age occupation         state  dateHired
0  5689  Brent  35.0   Engineer  Pennsylvania 2004-04-25
1  8998   Rick   NaN   Engineer     Wisconsin 1992-01-30
2  3456   Barb   NaN        NaN       Florida 1983-10-17
```  

As it can be seen, even though the 3 dates were initially in 3 different formats, they are all standardized now and recognized as dates by Pandas!  

## Loading from a JSON File 

> More on this can be found on [stackoverflow](https://stackoverflow.com/questions/49505872/read-json-to-pandas-dataframe-valueerror-mixing-dicts-with-non-series-may-lea).  

It's possible to quickly load a file cotaining a JSON string; if its basic, its as simple as:
```
df = pandas.read_json('someFile.json')
```  

Unfortunately, sometimes the JSON is a bit more complex (i.e., the data you are looking for is in a JSON array). If this is the case you can do something like this (the example assumes the data is in an array 'results'):  
```
import pandas 
import json

if __name__ == '__main__':
	
	data = json.load(open('someFile.json'))
	df = pandas.DataFrame(data["results"])
```  

## Copying DataFrames

You will often have to copy an entire - or part - of a DataFrame to another dataframe. 

Just coping a dataframe with only an equals sign (`someNewDataFrame=someExistingDataFrame` **or** `someNewDataFrame = someExistingDataFrame[:]`) **DOES NOT COPY BY VALUE** - it copies via address.  This means if you change one, you change the other.  To overcome this, use the `copy()` function.

That said, there are some methods that do seem to copy by value:  
* `someNewDataFrame = someExistingDataFrame[['col_A', 'col_B','col_C']]`
* `someNewDataFrame = someExistingDataFrame.ix[0:10,0:2]`

It seems `someNewDataFrame = someExistingDataFrame[0:4]` copies by memory location _but_ `someNewDataFrame = someExistingDataFrame[0:4].copy()` seems to copy values.  

Finally, if the dataframe is passed as a variable to a function and then is manipulated there, it will change the dataframe in the calling function as well - so its passed by reference there too.  

What does this all mean? Always, **ALWAYS** use `.copy()` if you are making a sub-dataframe and you care about the integrity of the first dataframe.  

`.copy()` can be used in a variety of situations - so if you are doing this
```
someNewDataFrame = myFirstDF
...
someNewDataFrame = myFirstDF[0:10]
...
someNewDataFrame = myFirstDF.iloc[0:10,0:2]
...

```

Its best to do this instead:  
```
someNewDataFrame = myFirstDF.copy()
...
someNewDataFrame = myFirstDF[0:10].copy()
...
someNewDataFrame = myFirstDF.iloc[0:10,0:2].copy()
...
```

!> If you are creating a sub-dataframe from a larger dataframe **and** you do not want changes in one to reflect changes in the other, use `.copy()`.  
	
> It should be noted that _if_ you create a sub-dataframe from another dataframe using something like `myFirstDF[0:10]` via [basic row range selection](learn_to_code/python/scipy/pandas?id=basic-row-range-selection) and then you try to modify the sub-dataframe, Python will give you a warning about it. You can avoid the warning by using `copy()` in conjunction with the [basic row range selection](learn_to_code/python/scipy/pandas?id=basic-row-range-selection) to save to a new sub-dataframe, _then_ perform operations on that.  

## Clearing Out NULLs

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  Also note that we will import numpy specifically for this example as well, so place `import numpy` in the import section at the top of the code.  

Null values can cause problems in datasets - often, its recommended to simply drop all rows that have a null value in _any_ column. There are two main types of null values you must watch for:
* Python's native null is `None`.
* Numpy has its own version of null - `numpy.NaN` (not a number). This is important, as this is actually the most prevalent null in Pandas (as Pandas relies on numpy).

If we want to drop _all_ rows that have a null in _any_ column (be it `None` type or `numpy.nan`), we can do so by use of `.dropna()`: 
```
myFirstDF['col_E'] = ["chicken", "pig", numpy.NaN, numpy.NaN, "horse", None]
myFirstDF.iloc[1,0] = numpy.NaN
myFirstDF.dropna(inplace=True)
```  
* I made a new column (`col_E`) that had both `numpy.NaN` and `None` values to show it will work on both.  
* I altered row 1 / column 0 to include `numpy.NaN` for illustrative purposes.
* If you do not use `inplace = True` you must set some other dataFrame variable equal to this so the changes are captured (and `inplace` defaults to `False` here).  

!> Its often considered good practice to use `.dropna()` on datasets. 

**<font size="4">Get the Inverse of .dropna()</font>**  

Sometimes its beneficial to save the rows that are about to be eliminated with `.dropna()` before `.dropna()` is used.  To do so (using [boolean indexing](learn_to_code/python/scipy/pandas?id=advanced-row-selection-and-manipulation)):  
```
savedRowsWithNulls = myFirstDF[myFirstDF.isnull().any(1)]
```  

## Merging DataFrames

To merge a dataFrame means to have two dataFrames with at least one column (_or_ all or part of the index) that can act as a [primary key](databases/relational_databases/database_key_terms?id=primary-key).  Stated differently, you have a dataframe `A` and a dataFrame `B` that has related [primary keys](databases/relational_databases/database_key_terms?id=primary-key), but other columns may be unique to `A` or `B`; the entire point of a merge is to somehow combine these dataFrame so _all_ of the information (i.e. all columns across both dataFrames) is in a single dataFrame. 

> A merge is very similar to the relational database concept of a [SQL JOIN](databases/relational_databases/sql?id=inner-join-39clause39).  

Consider this example:  
```
	employeeDF = pd.DataFrame({'employeeID' : [17, 36, 78, 9, 3, 47, 92],
								'age' : [33, 57, 40, 19, 27, 22, 34],
	                            'name' : ['Brent', 'Rick', 'Barb', 'Jimmy', 'Chris', 'Stan', 'Robert'],
	                            'department' : ['Engineering', 'Operations', 'Operations', 'Engineering', 'Engineering', 'Management', 'Management']})
	
	employeeHomeDF = pd.DataFrame({'employeeID' : [98, 78, 47, 92, 36, 86],
								'state' : ['Wyoming', 'Florida', 'Colorado', 'New Jersey', 'Wisconsin', 'Ohio'],
	                            'address' : ['123 Wyomissing Ave', '456 Yankee Dr', '789 Founders Ln', '012 Rosetta Ave', '345 Green Bay Ct', '678 Scab Ln']})
			
	print employeeDF
	print "-------------------" 
	print employeeHomeDF
	print "-------------------"
	
	targetDF = employeeDF.merge(employeeHomeDF,how='left', on='employeeID')
	
	print targetDF
```  
* There are two dataFrames, `employeeDF` and `employeeHomeDF`.  
 * Both share a column that could be considered a [primary key](databases/relational_databases/database_key_terms?id=primary-key): `employeeID`, which is a unique identifier for an employee.  
 * There are some IDs that are in one dataFrame but not the other; this is done on purpose.  
* The `.merge()` itself is done in `employeeDF.merge(employeeHomeDF,how='left', on='employeeID')`
 * To merge, you call the `.merge()` method from one or the other dataFrames.
 * The first parameter is the _other_ dataFrame.
 * The second is the 'how' parameter (see below).  
 * The third is the `on` parameter (see below).
 
**<font size="4">More on the 'on' Parameter</font>**

The `on=` parameter specifies the columns in _both_ of the dataframes that act as [primary keys](databases/relational_databases/database_key_terms?id=primary-key) and should be joined.  _Both_ dataFrames need to have these columns named **exactly** the same.  

In our example above, we simply set `on='employeeID'`, as `employeeID` is A) a column in _both_ dataFrames and B) considered a [primary key](databases/relational_databases/database_key_terms?id=primary-key) in _both_ dataFrames.  We could have also used a list to denote _multiple_ primay keys a la `on=['employeeID', 'name']` (this is simply an example of how it could be done, it will not actually work in the example above).  

You can omit the `on=` parameter completely, but if you do, the `.merge()` will rely on the [indicies](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes) **as well as any commonly named columns** of the dataFrames.  This can lead to undesirable results; however, you can get around the column names being included (so only the indicies are used) if you specify the parameters `left_index = True, right_index = True` (**both** must be present, and the indicies _must_ be named the same).  


**<font size="4">More on the 'how' Parameter</font>**

The 'how=' parameter is very similar to [LEFT/RIGHT/INNER/OUTER](databases/relational_databases/sql?id=inner-join-39clause39) joins in a relational database.  Here is what each mean:
* <font color="purple">left</font> - `how=left` will use _all_ of the rows of the 'left' dataFrame (the left dataFrame is the one whose method is being called); if there is a match using the `on=` parameter in the other dataFrame, the related data will be saved to the column for that row; if there is no match, the row available in the 'left' dataFrame will be there but the missing columns will be filled with `numpy.NaN`. If there are rows in the 'right' dataFrame that do not exist in the 'left', these rows from the 'right' dataFrame will not be saved at all.  
* <font color="purple">right</font> - `how=right` will use _all_ of the rows of the 'right' dataFrame (the right dataFrame is the one that is sent as a parameter in the `.merge()` method); if there is a match using the `on=` parameter in the other dataFrame, the related data will be saved to the column for that row; if there is no match, the row available in the 'right' dataFrame will be there but the missing columns will be filled with `numpy.NaN`. If there are rows in the 'left' dataFrame that do not exist in the 'right', these rows from the 'left' dataFrame will not be saved at all.  
* <font color="purple">inner</font> - `how=inner` will use _only_ the rows that are matched in _both_ dataFrames; if there are rows in the 'left' dataFrame but not the 'right' one (or vice-versa), these rows will not be represented at all. 
* <font color="purple">outer</font> - `how=outer` will use _all_ the rows in _both_ dataFrames, regardless if they match or not.  If they do match, all relevant column data will be saved, if they do not, all available column content will be saved, and the data that is not available after the match will be filled in with `numpy.NaN`.  
---

# Columns in DataFrames  

## Manipulating Column Names

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

You will frequently need to interact with the DataFrame on the column level: you will need to get column names, rename columns, or delete (or re-order) columns.

**<font size="4">Getting Column Names</font>**  

We can get the column names like so:  
```
myColumns = myFirstDF.columns.values
```  
* This gives a list of column names.  

You can also specify the order, if you know the position:
```
mySpecificColumnNames = myFirstDF.columns.values[[3,0,1]]
```  

From here, you can add on column names and then print the dataFrame, with the column names in that order:
```
mySpecificColumnNames = np.append(mySpecificColumnNames,['col_C'])
print myFirstDF[mySpecificColumnNames]
```  

prints:
```
      col_D  col_A  col_B col_C
0  0.036003     17    one    aa
1 -0.086413     36    two    aa
2 -1.440147     78  three    aa
3 -1.341464      9   four    bb
4  1.358462      3   five    bb
5 -0.017827     47    six    aa
```  

**<font size="4">Renaming Columns</font>**

There are two options to renaming colummns in Pandas. The first is probably the most straightforward:  
```
myFirstDF.rename(columns = {'col_B':'columnB', 'col_D':'columnD'}, inplace = True)
```  
* Renames columns `col_B` and `col_D`.  

This is done in-place, but if you wanted to create a new dataframe and keep the first dataframe as-is, you could do this:
```
myOtherDF = myFirstDF.rename(columns = {'col_B':'columnB', 'col_D':'columnD'}, inplace = False)
```  

The second method of re-naming columns is as so:  
```
myFirstDF.columns = ['col_A', 'columnB', 'col_C', 'columnD']
```  

The second method requires two things:  
* You must absolutely know the current order of the columns (usually they are in the order you create them, but you should check to make sure).  
* You must know you are actually changing all column names, even if you are 'changing' them to the same thing.  

The first method is usually preferred, unless you are changing _all_ column names.  

## Adding / Removing Columns 

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

**<font size="4">Add Columns</font>**

To add a column, simply create it (to an _existing_ dataframe) like so:  
```
myFirstDF['col_E'] = 0
```
* This adds a column `col_E` and sets all values to 0.  

You do not have to add a [scalar](learn_to_code/machine_learning/ml_key_terms?id=scalar) as above; you can also set it equal to a list, _provided_ the number of items in the list match the count of the number of rows in the dataframe / slice of dataframe:  
```
someList = [3, 6, 9, 12, 15, 18]
myFirstDF['col_E'] = someList 
```  

**<font size="4">Removing Columns</font>**

There are two ways of deleting a column in Pandas.  The first is straightforward:
```
del myFirstDF['col_C']
```  
* Deletes column `col_C`  

The second is more a roundabout way of doing it but also works: simply name all of the other columns you wish to keep and omit the one you do not:  
```
myFirstDF = myFirstDF[['col_A', 'col_B', 'col_D']]  
```
* Deletes column `col_C` through omission.  

This could potentially require more work, but if you would like to get rid of multiple _known_ columns at once this is just as easy to do; in addition, this method re-orders the columns as well.  So, if you know the columns you wish to keep beforehand _and_ you are removing muptiple columns at once _or_ you want to re-order the columns anyway, this second method may be a better option.  If you are just removing one column and you do not care if the order of the columns remain the same, its probably best to go with the `del` method as that is probably more efficeint.  

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

An <font color="purple">index</font> (aka a <font color="purple">label</font>) is much like a [primary key](databases/relational_databases/database_key_terms?id=primary-key) in [SQL](databases/relational_databases/sql) - they identify a record in a dataframe. 

Much like primary keys, the <font color="purple">index</font>:
* can be an number or a string.  
* can be made up of multiple fields.  

But unlike a [primary key](databases/relational_databases/database_key_terms?id=primary-key), an <font color="purple">index</font> _does not have to be unique_ (although its usually beneficial if it is).  

Every dataframe in Pandas has an <font color="purple">index</font> or <font color="purple">label</font> (even if its autopopulated), and most operations (like [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc)) actually work using the <font color="purple">index</font> as an anchor.  And example of an index is found in the [basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe): 
```
   col_A  col_B col_C     col_D
0     17    one    aa  0.258230
1     36    two    aa  0.335634
2     78  three    aa  0.917162
3      9   four    bb  0.320239
4      3   five    bb  1.241430
5     47    six    aa  1.617027
```  
* The numbers `[0, 1, 2, 3, 4, 5]` on the left is the auto-generated <font color="purple">index</font>.


> If you do not specify the <font color="purple">index</font>, Pandas will use an auto-incrementing integer <font color="purple">index</font> as a default <font color="purple">index</font>.

## Setting The Index

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

To set an index using a pre-existing column in the dataframe, use `set_index` like so:  
```
myFirstDF.set_index(['col_A'], append = True, inplace = True, drop = True) 
```  
* `append = True` (Default: `False`) means it will add to the current index. If this is false it will replace whatever <font color="purple">index</font> is there.  
* `inplace = True` (Default: `False`) means the column set is done in-place (so you do not have to set it to anything).  
* `drop = True` (default: `True`) drops the <font color="purple">index</font> as a column - otherwise, the <font color="purple">index</font> would be copied into the <font color="purple">index</font> _and_ remain as a column.

Before the index was set there was a default index: 
```
   col_A  col_B col_C     col_D
0     17    one    aa  0.258230
1     36    two    aa  0.335634
2     78  three    aa  0.917162
3      9   four    bb  0.320239
4      3   five    bb  1.241430
5     47    six    aa  1.617027
```  

After the index was set, `col_A` appeared as the index:  
``` 
       col_A  col_B col_C     col_D
col_A                              
17        17    one    aa  0.258230
36        36    two    aa  0.335634
78        78  three    aa  0.917162
9          9   four    bb  0.320239
3          3   five    bb  1.241430
47        47    six    aa  1.617027
```  

> Note in the first example there is no header above the index, but in the second, `col_A` is there and its also 'closer' to the index values than the column headers are to the column values.  This goes to show that indexes _can_ have a label, but default to no label.  Also, the visual is a cue that denotes the index when printed!  

## Multiple Indicies

Its also possible to set _multiple_ indicies: for example we could set a combination of `col_A` and `col_C` like so:
```
myFirstDF.set_index(['col_A', 'col_C'], inplace=True)
```  

Which would give:  
```
             col_B     col_D
col_A col_C                 
17    aa       one -0.420524
36    aa       two  1.652488
78    aa     three  1.226057
9     bb      four  1.006307
3     bb      five  1.843079
47    aa       six -0.460161
```  
* As it can be seen, `col_A` and `col_C` is now the _set_ of indicies for this dataframe.  

## Setting Index Names

To set the name of an index where there is only _one_ index, you can do this: 
```
myFirstDF.index.name = 'employeeID'
```  

However, if you have _multiple_ indicies, you will need to set it like this:
```
myFirstDF.index.set_names(['employeeID', 'name'], inplace=True)
```  
* `inplace=True` seems to be required.  
* This sets two index names.
* Please note this does _not_ work with the [basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as that only has one index (so `name` will bomb out); this is just an example.  

## Getting Index Values 

To get the values of the <font color="purple">index</font>, use `.index.values` like so
``` 
listOfIndexValues = myFirstDF.index.values
```  

## Re-Setting an Index

Sometimes its important to re-set the <font color="purple">index</font> if the <font color="purple">index</font> values are meaningless to you (after a dataframe concatenate when the dataframes use auto-incremental integers is an example of when this could be done).  The `reset_index` method resets the index to auto-incrementing integers. Here is an example using the [basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe): 
```
myFirstDF.reset_index(drop=True,inplace=True) 
```  
* `drop = True` drops the <font color="purple">index</font> as a column - otherwise, the <font color="purple">index</font> would be moved to a column (the default is `False`).  
 * If `drop = False` and the index has no name its simply called 'index'; otherwise, its name transfers to a column name.  
* `inplace=True` means the column set is done in-place (so you do not have to set it to anything). 

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


# Basic DataFrame Data Manipulation 

## Before We Start (Data Manipulation)

> Do not feel overwhelmed if you dont understand everything in these examples just yet - you will, given time.  Just tuck this knowledge away for later when the times comes to use it.  

Before we get into how we can manipulate these dataframes to return subsets of data, know you can do a few things on _any example_ in this section:
* Set them equal to a new dataframe to create a sub-dataframe.  
 * Use [.copy()](learn_to_code/python/scipy/pandas?id=copying-dataframes) on them to create a new independent dataframe.
   * Example: `someNewDataFrame = myFirstDF.iloc[0:10,0:2].copy()`
* Print them.  
 * Example: `print myFirstDF.loc['Billy':'Ray',]`
* Set them equal to a [scalar](learn_to_code/machine_learning/ml_key_terms?id=scalar).  
 * Example: `myFirstDF['col_E'] = 0`
* Set them equal to another, equally sized object: 
 * This <font color="red">only</font> works if the objects are of the sime size (number of rows and columns are the same).  
 * Example: `someOtherDataFrame.iloc[0:2, :1] = myFirstDF.iloc[0:2, :1] + 10`
* Using [math with different subframes](learn_to_code/python/scipy/pandas?id=math-with-dataframes) 
 * Example: `someOtherDataFrame.iloc[0:2, :1] = myFirstDF.iloc[:2, :1].values + myFirstDF.iloc[4:, :1].values`
 * You can do any math and it will translate to the entire matrix, provided the dimensions are the same.
* Interacting with [apply()](learn_to_code/python/scipy/pandas?id=sending-dataframe-to-functions-apply), which gives your homegrown function the appearance of [vectorization](learn_to_code/machine_learning/ml_key_terms?id=vectorization) (although it will not actually be vectorized). 

## Heads and Tails

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

To store the first `n` rows to another dataFrame, use `.head(n)` like so:
```
someOtherDataFrame = myFirstDF.head(2).copy()
print someOtherDataFrame 
```
* This saves the first two rows to `someOtherDataFrame`
* Its a good practice to use `.copy()` when copying to a new dataFrame (to completely avoid the chance of copying by reference).  

Conversely, you can store the last `n` rows to another dataFrame with `.tail(n)`:  
```
someOtherDataFrame = myFirstDF.tail(2).copy()
print someOtherDataFrame 
```  

## Basic List Column Selection  

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

The basic way to interact with the columns (but _not_ rows; for rows use [basic row selection](learn_to_code/python/scipy/pandas?id=basic-row-range-selection) **or** [loc or iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc))) via a list can be done like so:  
```
myList = ['col_B', 'col_C']
print myFirstDF[myList]
```  

This yields:
```
   col_B col_C
0    one    aa
1    two    aa
2  three    aa
3   four    bb
4   five    bb
5    six    aa
```  

A great way to introduce a new column _and_ set it to a default is using the column selection; simply type the new column name (it does _not_ have to exist) and set it equal to something like so:  
```
myFirstDF['col_E'] = -1
print myFirstDF 
```  

yields:  
```
   col_A  col_B col_C     col_D  col_E
0     17    one    aa  0.536528     -1
1     36    two    aa  0.218736     -1
2     78  three    aa -0.335546     -1
3      9   four    bb  0.884048     -1
4      3   five    bb  1.252163     -1
5     47    six    aa -0.400868     -1
```  

As a matter of fact, so long as the count of the right side equals the count of the left, you can be a bit more complex.  As an example:
```  
newValues = [60, 120, 180, 240, 300, 360]
myFirstDF['col_E'] = newValues
```  

Yields:
```
   col_A  col_B col_C     col_D  col_E
0     17    one    aa  0.401776     60
1     36    two    aa  0.368650    120
2     78  three    aa  0.243991    180
3      9   four    bb -1.103094    240
4      3   five    bb  0.485902    300
5     47    six    aa  0.087014    360
```  

!> An important takeaway from this is: so long as both sides of the `=` have the same count of objects in both length * width (in the above case thats 1 (column) * 6 (rows)), you can do aStanome stuff like this.  This is a part of a larger concept called [vectorization](learn_to_code/machine_learning/ml_key_terms?id=vectorization), which basically means 'avoid a for loop directly in Python'.  Usually, [vectorized code](learn_to_code/machine_learning/ml_key_terms?id=vectorization) takes advantage of optimized code in a more base language, meaning it has great performance.  

## Basic Row Range Selection

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

The basic way to interact with the rows via ranges (but _not_ columns; for columns use [basic column selection](learn_to_code/python/scipy/pandas?id=basic-list-column-selection) **or** [loc or iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc)) is to simply use a range with the dataframe, like so:  
```
print myFirstDF[:] 
```  
* Simply putting a `:` denotes 'all' rows.  


We can specify rows too, like so:  
```
print myFirstDF[0:4]
```  
* This prints rows `[0, 1, 2, 3]` (_excluding_ `4`).  

Omitting the first `0` means its implied there is a `0` there (and omitting the number after the `:` implies 'get the positions until the end'):  
```
print myFirstDF[:5]
```  

prints:  
```
   col_A  col_B col_C     col_D
0     17    one    aa -0.615249
1     36    two    aa  0.158491
2     78  three    aa -0.623237
3      9   four    bb -0.949267
4      3   five    bb -0.543275
```  
* Note row 5 is missing (the endpoint is _not_ inclusive).  

Going beyond the acceptible range does _not_ yield an error, it just converts it to `X:`: 
```
print myFirstDF.iloc[0:500]
```  

Yields: 
```
   col_A  col_B col_C     col_D
0     17    one    aa  0.238099
1     36    two    aa  0.896756
2     78  three    aa  1.130712
3      9   four    bb -0.189086
4      3   five    bb  1.326226
5     47    six    aa  0.538119
```  

Negative numbers _go backwards from the last item_.  For example:  
```
myFirstDF[-4:-1]
```  

yields:  
```
   col_A  col_B col_C     col_D
2     78  three    aa  1.200252
3      9   four    bb -0.394801
4      3   five    bb -1.396570
```  
* For the beginning number: since the number of rows = 6, (6-4) = 2, so start at index 2.  
* For the ending number: since the number of rows = 6, (6-1) = 5, so end at index 5 (and since the endpoint is not inclusive, the last one to be included is position 4).  
* Note that, at least for the first number, going past the start just starts it at 0 (much like if you go over for the last position).  

If the [indexes](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes) are _not_ integers, this logic still works; however, you can _also_ specify the actual label.  For example, we can [alter the index](learn_to_code/python/scipy/pandas?id=setting-the-index) by setting it to `col_C` and then do some fun things with the range:  
```
myFirstDF.set_index(['col_B'], append = False, inplace = True, drop = False)
print myFirstDF['two':'four']
```

This yields:  
```
       col_A  col_B col_C     col_D
col_B                              
two       36    two    aa -0.813242
three     78  three    aa  1.559376
four       9   four    bb  0.658825
```  
* Note that when using _labels_ and not the integer position, the endpoint is _inclusive_ (i.e. it did not lop off 'four' as it did when we were straight up using integers).  

!> It seems impossible to use ranges in conjunction with columns with this basic selection; if you wish to do that, use [loc or iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc).  

## Granular Selection: loc, iloc

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

A basic way to gather rows from a dataframe is `myFirstDF[0:4]`, which will return a dataframe of the first 4 rows (rows `[0, 1, 2, 3]` are returned).  This is OK if we know the position of rows we want, but oftentimes we will have to search through the dataframe for rows that will exist in nondeterministic / random spots.  This is where more advanced row selction methods come into play: the `ix`, `loc`, and `iloc` functions.  These functions can specify _exact_ rows - even if they are randomly found in the database - _as well as_ specific columns.

The functions `ix`, `loc`, and `iloc` are almost interchangeable, but there are differences:   
* `loc` works on labels in an index.  
* `iloc` works on the _positions_ of the index (so it only takes integers).  
* `ix` tries to work like `loc` but falls back to `iloc` if the label is not found.
 * `ix` is depricated as of pandas 0.20.0 (which was released on 2017-05-05); it should not be used moving forward.  
 * `ix` can be tricky, as if the label IS an integer, `ix` will only use label-based indexing and not use position based.  
 
> Generally speaking, I like to use `loc` as it is a bit more general use when compared to `iloc` - and specifically, `iloc` does _not_ work with [boolean masks](learn_to_code/python/scipy/pandas?id=the-basics-of-advanced-row-manipulation).  I use `iloc` when I know the _specific_ position of the data _or_ when doing something like sampling for testing purposes.  The rule of thumb is: **use iloc for positions and loc if you want to specifically reference a label / index OR you are using [boolean indexing](learn_to_code/python/scipy/pandas?id=pandas-sql-39where39-boolean-indexing)**.  

## Granular Selection: Lists 

At their base, these functions take two 'arguments': a [list](learn_to_code/python/python_basic?id=lists) of rows followed by a [list](learn_to_code/python/python_basic?id=lists) of columns. As an example (and using the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base), we can return the first, third, and fourth rows of our data as well as only columns `col_B` and `col_C` like so:
```
myFirstDF.loc[[0,2,3],['col_B', 'col_C']] 
```  

We could _also_ return just one column as well:  
```
myFirstDF.loc[[0,2,3],'col_C'] 
```  

We can specify the order of _both_ the rows and columns returned (so we can change around the order):  
```
print myFirstDF.loc[[3,0,2,1],['col_C', 'col_B']]
```  

We can also build [lists](learn_to_code/python/python_basic?id=lists) of included elements _and_ orders for both rows and columns in advance. Here, we create a row listing and a column listing beforehand and feed it to the dataframe later:  
```
myRows = [3,0,2,1]
myColumns = ['col_C', 'col_B']
print myFirstDF.loc[myRows,myColumns] 
```  

## Granular Selection: Multiple Indicies

Working on a dataframe with multiple [indicies] is a bit trickier. To reference the rows it will go from a simple [list](learn_to_code/python/python_basic?id=lists) to a list of [tuples](learn_to_code/python/python_basic?id=tuples) (you _cannot_ use a list of lists to reference the indicies).  Furthermore, you must know the specific order of the index beforehand (in my experience, [adding indicies using 'append = True'](learn_to_code/python/scipy/pandas?id=setting-the-index) simply concatenates the index onto the end, if that is what you are using, and the order will remain).  Fortunately, you can build the [tuple](learn_to_code/python/python_basic?id=tuples) beforehand (my example here, which uses the [basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe), shows an example of building the tuple beforehand):  
```
myFirstDF.set_index(['col_C'], append = True, inplace = True, drop = False)

three = 3
bee="bb"
firstTuple = (three, bee)
myRows = [firstTuple,(0,'aa'),(2,'aa'),(1,'aa')]
myColumns = ['col_C', 'col_B']
print myFirstDF.loc[myRows,myColumns]
```  
* `myRows` is a list of tuples (as denoted by `(` and `)`).  
* Since Pandas creates a default index, I simply kept it and appended `col_C` as the other index; so the tuple is `(defaultIntegerIndexThatPandasSupplies, col_C)`.  

If you reference a [tuple](learn_to_code/python/python_basic?id=tuples) that is _not_ an actual index when using `loc`, Pandas currently throws a `FutureWarning`, telling you that this will outright be an error in the future (and that you can use `.reindex()` as an alternative). For now it will simply _force_ the index in and give `NaN` for all values, but in this future this _will_ be an error.  

## Granular Selection: Ranges 

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

!> Under some circumstances, you _cannot_ mix [lists](learn_to_code/python/scipy/pandas?id=granular-selection-lists) with ranges - i.e., rows cannot be referenced as a list and columns a range or vice-versa in one line of code.  

> Everything that was true for [basic row selection via range](learn_to_code/python/scipy/pandas?id=basic-row-range-selection) is _also_ valid here; the only difference is here it possible to operate on the columns as well.  

In addition to [listing specific indexes that reference rows](learn_to_code/python/scipy/pandas?id=granular-selection-lists), we can specify ranges like so:
```
myFirstDF.iloc[:,:] 
```  
* Simply putting a `:` denotes 'all', be it rows or columns.  


We can specify rows / columns too, like so:  
```
print myFirstDF.iloc[0:4,0:2]
```  
* This prints rows `[0, 1, 2, 3]` (_excluding_ `4`) and the first two columns (`[0, 1]`, but _excluding_ `2`).  
* Note that I used [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) here; since [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) references positions and _not_ labels this will always work (so long as the range is valid).   
 * If you tried to use [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) here, you need to _make sure_ the row indexes are an integer _as well as_ the column names; if both are not, using `loc` here will error out (the columns are usually where this will fail).  
   * That said, so long as you kept the columns as lables _or_ you used `:`, you will be fine.  

Omitting the first `0` means its implied there is a `0` there (and omitting the number after the `:` implies 'get the positions until the end'):  
```
print myFirstDF.iloc[:5,1:]
```  

prints:  
```
   col_B col_C     col_D
0    one    aa -1.291353
1    two    aa -1.010290
2  three    aa -0.580483
3   four    bb  0.082658
4   five    bb  0.613577
```  
* Note `col_A` is missing (as we specified `1:` for columns which does not include `0`).  
* Note row 5 is missing (as when using [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc), the endpoint is _not_ inclusive).  


So far in this secdtion we have only used [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc); that said, [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) is also available to us. With `loc`, we are not limited to integers, but it must be noted that the value MUST exist; for example if the [index](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes) is 'names' and the list is Sally/Billy/Bob/Ray and stored in a dataframe `df`,  `df.loc['Billy':'Ray',]` or `df.loc['Billy':,]` should work, but `df.loc['Billy':'John',]` will fail as 'John' is not in the index.  Also note about [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) that 'Sally':'Bob' will include Bob as well (whereas, when using [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) and using `:`, it does not include the endpoint).  

To test the above, run:  
```
df = pd.DataFrame({'names' : ['Sally', 'Billy', 'Bob', 'Ray'],
							'job' : ['engineer', 'lawyer', 'accountant', 'police officer'],
							'randomNumber' : np.random.randn(4)})		

df.set_index(['names'], inplace=True)

print df
print df.loc['Billy':'Ray',]
print df.loc['Billy':,]
print df.loc['Sally':'Bob',]
print df.loc['Billy':'John',]
```  

## Granular Selection: Ranges & Multiple Indicies 

Ranges with [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) and [multiple indicies](learn_to_code/python/scipy/pandas?id=multiple-indicies) work differently than simply [listing the tuples](learn_to_code/python/scipy/pandas?id=granular-selection-multiple-indicies). Much like [using loc with a range and a single index](learn_to_code/python/scipy/pandas?id=granular-selection-ranges), the range edges are _inclusive_ (as opposed to [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc), which includes the first number but excludes the last), but the difference is what happens when the tuple does not exist - it does not error out, but seems to stop on a _portion_ of the tuple (which seems like its unpredictable).  An example: 
```
	df = pd.DataFrame({'names' : ['Sally', 'Billy', 'Bob', 'Ray'],
								'job' : ['engineer', 'lawyer', 'accountant', 'police officer'],
								'pet' : ['dog', 'cat', 'bird', 'penguin'],
								'randomNumber' : np.random.randn(4)})	
	
	df.set_index(['names'], inplace=True, append = True)
	
	print df
```  

This prints:
```
                    job      pet  randomNumber
  names                                       
0 Sally        engineer      dog     -0.582169
1 Billy          lawyer      cat     -0.129039
2 Bob        accountant     bird      0.548325
3 Ray    police officer  penguin      0.788182
```  

Recall that multiple indicies rely on [tuples](learn_to_code/python/python_basic?id=tuples).  In this example, we add an additional index with [set_index (append = True)](learn_to_code/python/scipy/pandas?id=setting-the-index) and then printed out a few of the rows.  One thing to note (as noted before) is that the range is _inclusive_ of the endpoints when using [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc); for example, this code
```
df.loc[(0, 'Sally'):(2, 'Bob'),'pet':'randomNumber']
```  

would yield:  
```
          pet  randomNumber
  names                    
0 Sally   dog     -0.582169
1 Billy   cat     -0.129039
2 Bob    bird      0.548325
```   

This is inclusive of _both_ endpoints (as opposed to [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc), which would include the beginning but not include the end).  

Curiously, if we use a [tuple](learn_to_code/python/python_basic?id=tuples) that is _not_ valid (say, `(2, 'Ray')`), the range seems to key off just the first part of the index (i.e. `2`): 
```
print df.loc[(1, 'Billy'):(2, 'Ray'),]
```  

yields:  
```
                job   pet  randomNumber
  names                                
1 Billy      lawyer   cat     -1.235578
2 Bob    accountant  bird      0.597756
```  

This seems unpredictable, so I would definitely make sure the tuple exists as an error is **not** thrown (nor even a warning).  

> The range for [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) and multiple indicies is the same as if there was a single index (as [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) works off position and not the index label, the index label does not matter with [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) therefore nothing special needs to be done, outside of not going out of range).  

## Granular Selection: Mixing Ranges and Lists

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

When using [loc or iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) you can use a combination of a [basic column selection via list](learn_to_code/python/scipy/pandas?id=basic-list-column-selection) and a range for the rows in [loc or iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc). As an example:
```
print myFirstDF[['col_B', 'col_C']].iloc[1:3,]
```  

Above, we listed the two columns using the basic approach, and then listed the range (and did not specify columns, which will be carried over from the basic selection) in `iloc`.  

## Basic Cell Selection

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

If we wanted to interact with just _one_ cell, we could do so with [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc).  For reference here is the full dataframe:
```
   col_A  col_B col_C     col_D
0     17    one    aa -1.175804
1     36    two    aa -0.776657
2     78  three    aa  0.220885
3      9   four    bb -2.184492
4      3   five    bb -0.653453
5     47    six    aa  0.464495
```  

Lets start with `iloc` by getting the cell in row position 2, column position 0:
```
print myFirstDF.iloc[2,0]
```  
* This prints 78.  

We could also store a value to that position:
```
myFirstDF.iloc[2,0] = 54
```  

!> It does not appear that `iloc` can work like this in returning single cells.  


# Advanced Row Selection and Manipulation 



## The Basics of Advanced Row Manipulation

The critical, most powerful thing about these functions is it can take a list of booleans; for example, we can return the first, third, and fourth rows of our data as well as only columns `col_B` and `col_C` in a different manner than above like so: 
```
myBolValues = [True,False,True,True,False,False]	
print myFirstDF.loc[myBolValues,['col_B', 'col_C']]
```

This is known as a <font color="purple">boolean mask</font>.  <font color="purple">Boolean masks</font> allow for Pandas dataframes to be searched in a manner similar to a [WHERE](databases/relational_databases/sql?id=where-clause) statement in [SQL](databases/relational_databases/sql), which is called [boolean indexing](learn_to_code/python/scipy/pandas?id=pandas-sql-39where39-boolean-indexing) in Pandas.



## Pandas SQL 'WHERE' / Boolean Indexing  

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

!> The usage of [iloc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) is not allowed when utilizing <font color="purple">boolean indexing</font> - you _must_ use [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) instead!  

A Pandas dataframe can utilize something akin to a [SQL WHERE statement](databases/relational_databases/sql?id=where-clause) - returning the desired rows that meet certain criteria - utiliging a <font color="purple">boolean index</font>. <font color="purple">Boolean indexes</font> use <font color="green">boolean masks</font> to return specific rows. As a review, here is an example of a <font color="green">boolean mask</font> in action: 
```
myBolValues = [True,False,True,True,False,False]	
print myFirstDF.loc[myBolValues,['col_B', 'col_C']]
```  

Which would print: 
```
   col_B col_C
0    one    aa
2  three    aa
3   four    bb
```  

We can expand upon this <font color="green">boolean mask</font> idea and turn it into a list of boolean statements. For example, recall the [basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe), which gives us this dataframe: 
```
   col_A  col_B col_C     col_D
0     17    one    aa -0.632135
1     36    two    aa  0.024518
2     78  three    aa -0.492915
3      9   four    bb -0.497690
4      3   five    bb -0.709657
5     47    six    aa  0.178335
```  

Lets say we wanted to set `col_D` to -1 if `col_A` < 70 _and_ `col_C` == 'aa'; we could actually write a <font color="green">boolean mask</font> and then implement it in the <font color="purple">boolean index</font> like so: 
```
myBolValues = ((myFirstDF['col_A'] < 70) & (myFirstDF['col_C'] == 'aa'))
print myBolValues
myFirstDF.loc[myBolValues, ['col_D']] = -1
print myFirstDF
```  

!> `dataframe['specificColumn']` (when only returning _one_ column) actually turns the result into a [Series](learn_to_code/python/scipy/pandas?id=series), this opens up all kinds of possibilities to use [vectorization](learn_to_code/machine_learning/ml_key_terms?id=vectorization) to transform the data (for example, you can use `dataframe['specificColumn'].str.contains('x')` to return a list of booleans that denote if the associated string has an 'x' in it).  

This code saves a <font color="green">boolean mask</font> to `myBolValues` and then prints them (to show that its simply a list of booleans); from there, is applies that <font color="green">boolean mask</font> as a <font color="purple">boolean index</font> to the rows of `myFirstDF` and targets column `col_D` to set equal to `-1` of the match works. Sure enough, here is the reuslt: 
```
   col_A  col_B col_C     col_D
0     17    one    aa -1.000000
1     36    two    aa -1.000000
2     78  three    aa -1.231994
3      9   four    bb -0.665221
4      3   five    bb -0.590572
5     47    six    aa -1.000000
```  
* As it can be seen, the first, second, and last rows had `col_D` set to -1; row 2 was not set because `col_A` was 78 (greater than 70) and rows 3 and 4 were not set because there, `col_C` == 'bb'.  

> Instead of setting the found columns to a value, we could have also stored the results to a new dataframe or any number of other operations.  

!> Anything that returns a boolean list can be used, but the number of elements **MUST** match the rowcount that is in the dataframe calling `loc`!   

Its also possible to use the <font color="green">boolean mask</font> in-line; in addition, we can set _multiple_ columns at once.  Take a look at this example:  
```
myFirstDF.loc[((myFirstDF['col_A'] < 70) & (myFirstDF['col_C'] == 'aa')), ['col_A', 'col_D']] = [-2, -1]
```  
* Here, we moved the <font color="green">boolean mask</font> to be in-line; in addition, we set _both_ `col_A` and `col_C` if the match works.  This produces: 
```
   col_A  col_B col_C     col_D
0     -2    one    aa -1.000000
1     -2    two    aa -1.000000
2     78  three    aa -0.139538
3      9   four    bb -0.493410
4      3   five    bb -0.720880
5     -2    six    aa -1.000000
```  

!> Parenthesis `()` have been a hit or miss for me, personally. There have been times that, for whatever reason, wrapping the entire statement in parenthesis actually caused an error and removing it fixed the issue (while leaving individual boolean checks wrapped in parenthesis); other times, I had no issue with parenthesis. If you have an odd problem, try removing the outer parenthesis on the statement and see if it helps.

## Boolean DataFrames 

Its possible to use a boolean index on an entire dataframes without the use of iloc or loc; to do so, you need to figure out if you are comparing columns or rows.  _If_ you are comparing row results (which is more common), then you need to make sure that the number of rows in your main dataFrame match that you are comparing (the column counts do not have to match, necessarily, we will get to that in a bit); conversely, if you are comparing column results, the column counts must match.

This all hinges off how boolean masks work in a dataFrame.  For example, if we simply created a boolean mask in a N**X**M dataFrame and then applied that to another (or even the same) dataFrame, it would look something like this:
```
mySecondDF = pd.DataFrame({'col_A' : [1, 1, 0],
                            'col_B' : [0, 1, 0]})	
myBooleanMask = (mySecondDF == 1)
print myBooleanMask
```

`myBooleanMask` is now a dataframe of 3**X**2 cells filled with boolean values:
```
0   True  False
1   True   True
2  False  False
```

If we applied this map _directly_ to another 3**X**2 dataFrame, it would print the values in that dataFrame _if_ the boolean mask was True; if False, it would print `numpy.NaN`. So if we did this:
```
	myFirstDF = pd.DataFrame({'col_A' : [17, 36, 78],
	                            'col_B' : np.random.randn(3)})	

	mySecondDF = pd.DataFrame({'col_A' : [1, 1, 0],
	                            'col_B' : [0, 1, 0]})	
	
	print (mySecondDF == 1)
	print myFirstDF[mySecondDF == 1]
```

would print:
```
   col_A  col_B
0   True  False
1   True   True
2  False  False


   col_A    col_B
0   17.0      NaN
1   36.0 -0.39864
2    NaN      NaN
```  

So if the boolean mask returns `True` (i.e. its value in that cell `==1`), its corresponding cell in `myFirstDF` will be properly displayed; if `False`, the corresponding cell in `myFirstDF` will be `numpy.NaN`.  There is some utility in this, but its not as helpful as some alternatives.  

One of the things that _can_ be seen is `(mySecondDF == 1)` translates to a dataFrame of booleans. DataFrames of booleans gain two special properties: `.any(axis=x)` and `.all(axis=x)`

**<font size="4">Boolean DataFrames: .any(axis=x)</font>**

When applied to a boolean dataframe, `.any(axis=x)` looks to see if there are _any_ `True` values across the axis (0 = columns, 1 = rows). So, for example, `.any(1)` would look across _all_ values in a single row (i.e. it would look at its counterpart across all columns) and see if there was _at least one_ `True` value (effectively, `.any(axis=x)` acts like an **OR** statement); if at least one was `True`, a `True` is returned for the _entire_ row (so effectively, each row is represented by either `True` or `False` when `axis=1` for `.any()`).  

Consider this example:
```
	myFirstDF = pd.DataFrame({'col_A' : [17, 36, 78],
	                            'col_B' : np.random.randn(3)})	

	mySecondDF = pd.DataFrame({'col_A' : [1, 1, 0],
	                            'col_B' : [0, 1, 0]})	
		
	myMask = (mySecondDF == 1).any(1)	
	print (mySecondDF == 1)
	print "----------------"
	print myMask
	print "----------------"
	print myFirstDF[myMask] 
```  

This prints: 
```
   col_A  col_B
0   True  False
1   True   True
2  False  False
----------------
0     True
1     True
2    False
dtype: bool
----------------
   col_A     col_B
0     17 -0.260710
1     36 -1.247249
```  

As it can be seen:  
* `(mySecondDF == 1)` is a boolean dataFrame which keeps the dimensions.
* `myMask` - which is `.any(1)` applied to `(mySecondDF == 1)` - collapses into a [Series](learn_to_code/python/scipy/pandas?id=series), with one data point representing the result of applying an **OR** across one row (to see this, apply an OR in the first printed section and compare to the values in the second printed section).  

The most suprising is the last revelation: applying the boolean mask `(mySecondDF == 1).any(1)` (which performs the **OR** and flattens the booleans to one value per row) actually _eliminates_ the row from the returned `myFirstDF` dataFrame slice (that is to say, the third row - which was `False` in the series - is omitted from print after the boolean mask was applied).  This is different from the direct application of the 'raw' boolean mapping, which simply turned individual cells to `numpy.NaN` for a False.

Using the `.any(axis=1)` method of a boolean dataFrame can allow for a rudimentary boolean mapping to be applied without the use of `loc` in a statement such as:
```
someOtherDataFrame = myFirstDF[(mySecondDF == 1).any(1)]
```  

So long as the number of rows returned in the boolean dataframe match that of its outside target, this will work.

> As stated in the [clearing out nulls](learn_to_code/python/scipy/pandas?id=clearing-out-nulls) section, one way to get all rows with at least one null is by using `myFirstDF[myFirstDF.isnull().any(1)]`, which uses the _same_ principle as mentioned here.

**<font size="4">Boolean DataFrames: .all(axis=x)</font>**

This works similarly to `.any(axis=x)` as above, but instead of deploying an **OR** across all rows (at least when axis = 1), it applies an **AND**.  For example: 
```
	myFirstDF = pd.DataFrame({'col_A' : [17, 36, 78],
	                            'col_B' : np.random.randn(3)})	

	mySecondDF = pd.DataFrame({'col_A' : [1, 1, 0],
	                            'col_B' : [0, 1, 0]})	
		
	myMask = (mySecondDF == 1).all(1)	
	print (mySecondDF == 1)
	print "----------------"
	print myMask
	print "----------------"
	print myFirstDF[myMask] 
```  

prints:
```
   col_A  col_B
0   True  False
1   True   True
2  False  False
----------------
0    False
1     True
2    False
dtype: bool
----------------
   col_A     col_B
1     36  0.437601
```  

Since there was only one row with _both_ values `True` (the second row, index = 1), only that row was returned.  

## Boolean Indexes: Lists

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

Its possible to utilize a list to help with making a statement that uses a boolean index more readable. For example, consider the following, which sets 'col_A' to -1 under certain criteria:
```
myFirstDF.loc[(((myFirstDF['col_B'] == 'two') | (myFirstDF['col_B'] == 'four') | (myFirstDF['col_B'] == 'six')) & (myFirstDF['col_C'] == 'aa')), 'col_A'] = -1
```  

We can make the above a bit more readable with `.isin()`:
```
myFirstDF.loc[((myFirstDF.isin({'col_B': ['two', 'four', 'six']})).loc[:,'col_B'] & (myFirstDF['col_C'] == 'aa')), 'col_A'] = -1
```  
* This is a bit better, but you are:
 * forced to mention `col_B` twice (once in the dictionary portion and once in the cumbersome `.loc[:,'col_B']` at the end). 
   * The `isin()` function returns _all_ columns in the dataframe so you must specify it in the backend [loc](learn_to_code/python/scipy/pandas?id=granular-selection-loc-iloc) call.  
 * We put the list in the middle of the line.
 
We can move the list to outside of the line like so:
```
myColBs = ['two', 'four', 'six']
myFirstDF.loc[((myFirstDF.isin({'col_B': myColBs})).loc[:,'col_B'] & (myFirstDF['col_C'] == 'aa')), 'col_A'] = -1
```  

The above still leaves in the cumbersome `.loc[:,'col_B']`; we can avoid all of this, though, by use of the following:
```
myColBs = ['two', 'four', 'six']
myFirstDF.loc[((myFirstDF['col_B'].isin(myColBs)) & (myFirstDF['col_C'] == 'aa')), 'col_A'] = -1
```  

## Boolean Indexes: Strings

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

Since `dataframe['specificColumn']` (when only returning _one_ column) actually turns the result into a [Series](learn_to_code/python/scipy/pandas?id=series), this opens up all kinds of possibilities to use [vectorization](learn_to_code/machine_learning/ml_key_terms?id=vectorization) to transform the data. One of those ways is via the `.str` method of a `Series` object.  

**<font size="4">Contains (finding Substrings)</font>**

To find substring matches in a column, you can use `.str.contains('x')`; for example, when `col_B` contains an 'o', 'col_A' is set to -1: 
```
myFirstDF.loc[(myFirstDF['col_B'].str.contains('o')), 'col_A'] = -1
```  

This will find all strings in `col_B` that contain an 'o' ('one', 'two', 'four').  This is the result:  
```
   col_A  col_B col_C     col_D
0     -1    one    aa -1.740358
1     -1    two    aa -0.819303
2     78  three    aa  0.496883
3     -1   four    bb -2.029327
4      3   five    bb -0.480626
5     47    six    aa -0.930173
```  

## Boolean Indexes: Nulls

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  Also note that we will import numpy specifically for this example as well, so place `import numpy` in the import section at the top of the code.  

Its possible to weed out nulls (in Python, its `None`).  It should be noted that often, `numpy.NaN` (not a number) is used as a stand-in for null - so we have to account for those too.

Here is an example where you can limit a field to _just_ the values that are null; for example, when `col_E` contains a null, 'col_A' is set to -1:
```
myFirstDF['col_E'] = [np.NaN, "pig", np.NaN, np.NaN, "horse", None]
myFirstDF.loc[(pd.isnull(myFirstDF['col_E'])), 'col_A'] = -1
```  
* Note that _both_ `None` _and_ `np.NaN` tripped `.isnull()`.  

Another way it could be done is using `.isnull()` directly (this simply sets `col_A` to -1 when True):  
```
myFirstDF['col_E'] = [np.NaN, "pig", np.NaN, np.NaN, "horse", None]
myFirstDF.loc[(myFirstDF['col_E'].isnull()), 'col_A'] = -1
```  

Conversely, if we wanted to find all rows that were _not null_, we could use (this simply sets `col_A` to -1 when True):  
```
myFirstDF['col_E'] = [np.NaN, "pig", np.NaN, np.NaN, "horse", None]
myFirstDF.loc[(myFirstDF['col_E'].notnull()), 'col_A'] = -1
```
* `(pd.notnull(myFirstDF['col_E']))` would have also worked here, but its a bit more wordy.  


**<font size="4">Eliminate All Nulls From All Columns</font>**

We can issue a _blanket_ statement that finds _all_ rows that have no nulls with this (this simply sets `col_A` to -1 when _the entire row_ is _not_ null):  
```
myFirstDF['col_E'] = [np.NaN, "pig", np.NaN, np.NaN, "horse", None]
myFirstDF.loc[(myFirstDF.notnull().all(1)), 'col_A'] = -1
```
* You could also specify _specific_ columns if you wanted too a la `myFirstDF[['col_A', 'col_B']].isnull().any(1)` if you wanted to do so.  

**<font size="4">Return All Rows With a Null In Any Column</font>**

If you want to return all rows with a null in _any_ column, you can use this (this simply sets `col_A` to -1 when _the entire row_ is _not_ null):  
```
myFirstDF['col_E'] = [np.NaN, "pig", np.NaN, np.NaN, "horse", None]
myFirstDF.loc[(myFirstDF.isnull().any(1)), 'col_A'] = -1
```  
* You could also specify _specific_ columns if you wanted too a la `myFirstDF[['col_A', 'col_B']].isnull().any(1)` if you wanted to do so.  

## Boolean Indexes: Opposite

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

You can use the `~` operator to take the opposite of the boolean result (so if something evaluates to `True`, it will evaluate to `False`, and vice-versa).  For example:  
```
myBooleanMask = pd.Series([False, True, True, False, True, True])
myBooleanMask2 = pd.Series([True, False, False, False, True, True])
print myFirstDF.loc[(myBooleanMask & ~myBooleanMask2),:]
```  

prints:
```
   col_A  col_B col_C     col_D
1     36    two    aa  1.701039
2     78  three    aa -1.000927
4      3   five    bb  1.077042
5     47    six    aa  1.508573
   
   
   col_A  col_B col_C     col_D
1     36    two    aa  1.701039
2     78  three    aa -1.000927
```  

As it can be seen, using `~` in front of a mask (specifically a [Series](learn_to_code/python/scipy/pandas?id=series), _not_ a [list](learn_to_code/python/python_basic?id=lists) will return the opposite boolean / will negate the boolean.  

## Boolean Indexes: Indexes

Oftentimes, the [index](learn_to_code/python/scipy/pandas?id=indexes-in-dataframes) is simply auto-populated and has no meaning. However, there are times when its properly set to a unique ID, unique date, etc. In these cases - when the index is meaningful - we can do a few more interesting things.

This boolean index sees if the second dataFrame's index is contained in the first; if so, that row is kept.  Example:
```
	myFirstDF = pd.DataFrame({'col_A' : [2, 4, 6, 8, 10, 12],
	                            'col_B' : ['one', 'two', 'three', 'four', 'five', 'six'],
	                            'col_C' : ['aa', 'aa', 'aa', 'bb', 'bb', 'aa'],
	                            'col_D' : np.random.randn(6)})

	mySecondDF = pd.DataFrame({'col_A' : [1, 4, 6, 9, 11],
	                            'col_B' : ['one', 'two', 'three', 'four', 'five'],
	                            'col_C' : ['aa', 'aa', 'aa', 'bb', 'bb']})
		
	myFirstDF.set_index(['col_A'], append = False, inplace = True, drop = True)
	mySecondDF.set_index(['col_A'], append = False, inplace = True, drop = True)
		
	print myFirstDF.loc[(myFirstDF.index.isin(mySecondDF.index)),:]
```  

prints:
```
       col_B col_C     col_D
col_A                       
4        two    aa  0.235966
6      three    aa  1.958883
```  

It should be noted that it doesnt even matter if the number of columns _or_ rows align; all that is important is if the index is included or not.  

We could have negated this too; so if we would have said:
```
print myFirstDF.loc[(~myFirstDF.index.isin(mySecondDF.index)),:]
```  

We could have ended up with all of the indicies in the first dataFrame that _were not in_ the second:
```
      col_B col_C     col_D
col_A                      
2       one    aa -0.699214
8      four    bb -1.113476
10     five    bb -0.935829
12      six    aa  1.247348
```  

## Sending DataFrame to Functions: apply()

When possible, you should use [vectorized](learn_to_code/machine_learning/ml_key_terms?id=vectorization) methods to process data in dataframes; that said, sometimes it is impossible to do so and you must perform some function in a non-vectorized fashion.

The below is an example of how this can be done. First, we create the function `ApplyAddition()` which accepts a single parameter (a series object); this function assumes the series has (at least) 2 columns and they are both numbers.

To actually apply this function, we call the `apply()` method of a dataframe object, which needs at least one parameter sent (namely, the function to call). In the below example we also set `axis = 1` which means 'create a series object and send each row through individually' (this must be done if you wish to send more than one column to the function - I usually do it by default). 

```
#this is the function that will be called from apply()	
def	ApplyAddition(seriesFromDataframe, x, y):
	#simply add the two rows together, add in x and y, and return the result
	# This does indeed come in as a Series object - if 'axis = 1', this is given a Series containing all values in a single row (across all sent columns); conversely, if this was 'axis = 0', for each column this would have sent the entire column
	#	and we would have worked on the entire column at once 
	return seriesFromDataframe[0] + seriesFromDataframe[1] + x + y

if __name__ == '__main__':

	myFirstDF = pd.DataFrame({'col_A' : [1, 3, 5, 7, 9, 11],
	                            'col_B' : ['one', 'two', 'three', 'four', 'five', 'six'],
	                            'col_C' : ['aa', 'aa', 'aa', 'bb', 'bb', 'aa'],
	                            'col_D' : np.random.randn(6), 
	                            'col_E' : [2, 4, 6, 8, 10, 12],})	
	print myFirstDF
	
	#send **ONLY** col_A and col_E to the function 'ApplyAddition' - add them together, add the arguments, and return the result to myFirstDF['newAddition']
	myFirstDF['newAddition'] = myFirstDF[['col_A', 'col_E']].apply(ApplyAddition, args= (1,20), axis = 1) 
	
	print myFirstDF

```

The above prints the following (note - since columns C and D are random, the results will be a bit different):
```
   col_A  col_B col_C     col_D  col_E  newAddition
0      1    one    aa  0.802939      2           24
1      3    two    aa -0.119004      4           28
2      5  three    aa -1.276887      6           32
3      7   four    bb  0.887372      8           36
4      9   five    bb  0.003627     10           40
5     11    six    aa -0.990602     12           44
```

Notes:  
* We created a slice of the original dataFrame `myFirstDF` and sent that instead of the entire dataFrame (as all that was needed were those two columns).  
 * Its good practice to do it this way.  
* The first argument of `.apply()` is the function name.  
* `args= (1,20)` are the arguments for the function.  
 * Note **it is expected that the first argument passed is the dataframe itself**, so any _additional_ arguments are passed with `args=`.  
   * Note the dataframe is not actually sent, but [Series](learn_to_code/python/scipy/pandas?id=series) slices of it are (see discussion below on `axis = 1`).  
 
A bit more on the `axis=1` parameter: this is specifying we want to send in the data by _row_, **not** by column.  Technically, function `ApplyAddition` will be called 6 times (once per row) to get the result (each time `ApplyAddition` is called, a [Series](learn_to_code/python/scipy/pandas?id=series) object - and when `axis=1`, this is comprised of all column values in a single row - is sent to `ApplyAddition`, which in turn references that as `seriesFromDataframe`).  If we had specified `axis = 0`, this would have meant we wanted to send each _column_ through the function, so this would mean `ApplyAddition` will be called 2 times (in once instance, `seriesFromDataframe` would represent the entirety of `col_A`, and the second would be `col_E`), it would add the first two rows + x and y, and then return that. Performing column work is helpful if you wish to get information for all sent columns, but usually, we are interested in rows instead.  

!> You can do something similar with for loops, but this makes things a little cleaner; regardless, you should use native functions - and avoid apply() - if at all possible, as it will not be [vectoized](learn_to_code/machine_learning/ml_key_terms?id=vectorization) and will thus not be optimized for speed.

---

# Math With DataFrames  

## Basic DataFrame Math

> We will use the [Basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe) as a base for this section.  

Often you will have to do some basic math on a dataframe; you can use [vectorization](learn_to_code/machine_learning/ml_key_terms?id=vectorization) (i.e. avoid 'for' loops for usually a dramatic increase in performance) to do these operations, provided that:  
A\. You are updating _all rows in a column_ with a [scalar](learn_to_code/machine_learning/ml_key_terms?id=scalar).  
B\. You are updating a _select portion_ of the dataFrame that has a dimension N**X**M with another object that also has dimensions N**X**M.  

An example would be multiplying all values of a column by `10`:  
```
myFirstDF['col_A'] = myFirstDF['col_A']*10
```  

You will sometimes have to update a select portion of the data (in some cases, you will have to use part of a dataframe to modify another dataframe); in order to do this, you will have to utilize the `.values` 'function'.  An example that uses the [basic DataFrame example](learn_to_code/python/scipy/pandas?id=bulding-a-basic-dataframe):
```
someOtherDataFrame = myFirstDF.copy()
someOtherDataFrame.iloc[0:2, :1] = myFirstDF.iloc[:2, :1].values + myFirstDF.iloc[4:, :1].values
```
* Note the `.values` bit is _critical_ here - do not forget it!
* `myFirstDF.iloc[:2, :1]` and `myFirstDF.iloc[4:, :1]` have the _same_ N**X**M dimensions (in this case, 2 rows by 1 column).  

We are trying to take a section of `someOtherDataFrame` and modify it with another part of a dataframe.  To do so, you _must_ use `.values` to extract the values _first_.  

---

# Sampling Data

To get a random subset of a dataframe, we can do something like this:
```
	myDataFrame = pandas.DataFrame({'col_A' : np.random.randn(12),
						'col_B' : np.random.randn(12)})	
	randomSample1 = myDataFrame.sample(n=9)
	randomSample2 = myDataFrame.sample(n=9, replace=True)

	print myDataFrame
	print '---------------------'
	print randomSample1
	print '---------------------'
	print randomSample2
```  

We can use the function `.sample(n=X)` (where `X` < number of rows in the dataframe) to randomly pick rows out of the dataframe. Be default, Pandas uses [without replacement](learn_to_code/machine_learning/ml_key_terms?id=without-replacement).  To use [with replacement](learn_to_code/machine_learning/ml_key_terms?id=without-replacement), set `replace=True` (as is done for `randomSample2`); you will note that in `randomSample2`, some of the rows / samples were probably repeated. Note that the replacement rule does _not_ effect subsequent usages of `.sample()`; so if you wish to sample without replacement across multiple runs, you may want to find another way to do so.

---

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

# Everyday Operations

These are things that I tend to do often in Pandas but are more or less one-offs.

## Converting to Numpy Array

Its possible to convert a pandas dataframe to a numpy array - to do so, just use the `.values` property. Here is an example that converts a pandas dataframe to a numpy array:
```
df = pd.read_csv('https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data', header=None)
	
newNumpyArray = df.iloc[0:100, 4].values
```

# Unfiled

```
	####
	#group by the call type and find the average of timeDelta
	tempSummary = tempDF[[groupByField, 'timeDelta']].groupby([groupByField]).mean()
	
	#get a count of all callTypes (and then re-name the column 'numCalls')
	countSummary = tempDF[[groupByField, 'timeDelta']].groupby([groupByField]).count()
	countSummary.columns = ['numCalls']

	#merge the count table in
	tempSummary = tempSummary.merge(countSummary,how='left', on=groupByField)
	
	#the groupBy field becomes the index, so pop it out into its own column again and then reset the index to the default incremental integers
	tempSummary[groupByField] = tempSummary.index.values
	tempSummary.reset_index(drop=True,inplace=True) 
	
	####
	tempTest = findStats(tempDF.sample(n=sampleSize), groupByField)
	tempTest['testNumber'] = x+1
	testResults = pd.concat([testResults, tempTest], sort=False)
	
	#the indexes are a bit wonky (they are all 0), so normalize with incremental integers
	testResults.reset_index(drop=True,inplace=True)	
	
	
	####
	someDF_Collection['timeDelta'].hist()
	someDF_Collection.hist(column='secondsBucket')
	plt.show()	#critical, otherwise it will not show
	####

	#####Other SQL like functions
	##joining via 'merge'
	######ONE MAJOR CAVEAT is that it will NOT match strings and integers - so if one column is an int make sure BOTH are
	#this can be done with .astype(int): myDataFrame['myID'] = myDataFrame['myID'].astype(int)
	######ANOTHER MAJOR CAVEAT is NULLS MATCH - so make sure to EXCLUDE them from at LEAST the secondary DF with a .notnull 
	left = pd.DataFrame({'myKey': ['foo', 'bar'], 'lval': [1, 2]})
	right = pd.DataFrame({'myKey': ['foo', 'bar'], 'rval': [4, 5]})
	aa = pd.merge(left, right, on='myKey')
	#multiple keys can be used too - I wont build it but here is an example:
	#aa = pd.merge(A_df, B_df, how='left', left_on=['A_c1','c2'], right_on=['B_c1','c2']) 
	
	##appending
	aa = dfTest.head(10)
	bb = aa.iloc[3,:] #take a random row
	aa = aa.append(bb,ignore_index = True) #the ignore index is important otherwise it will re-use the index which may not be desirable
	
	##Group by
	#its possible to group by categories and then apply a math operator
	#it seems to be the case that if any of the GROUP BY fields are NULL (np.nan), that entire row is skipped for consideration
	bb = pd.DataFrame({'A' : ['foo', 'bar', 'foo', 'bar',
								   'foo', 'bar', 'foo', 'foo'],
							'B' : ['one', 'one', 'two', 'three',
								   'two', 'two', 'one', 'three'],
							'C' : np.random.randn(8),
							'D' : np.random.randn(8)})	
	
	#the result in aa seems to be a new dataframe with TWO indexes - A and B
	aa = bb.groupby(['A','B']).sum()
	
	#####Sorting
	#sorting can be done on one or multiple columns at once - the following is set up so '1' has values 10-60 and '2' has 70-120
	aa = pd.DataFrame({'A' : [1, 1, 1, 2, 2, 2, 1, 1, 2, 2, 1, 2], 
					'B' : [10, 40, 20, 
						70, 100, 80,  
						60, 50, 
						120, 110, 
						30, 
						90]})	
	#do the sort - sort by B first, then A using ascending
	#'inplace = True' means you do NOT have to re-set the dataframe; in other words you can exclude the 'aa = ' at the beginning
	#inplace is helpful as it saves on memory and is available in a wide variety of functions for DataFrames 
	aa.sort(columns=['B', 'A'], ascending=True, inplace=True)	
	
	####SequenceMatcher - getting string matching percentages
	"""
	This is an example of how you can compare strings and see how much they match
	This also showcases how to apply a function to a dataframe that is nonstandard (as SequenceMatcher does not natively support dataframes) 
	"""
	aa = pd.DataFrame({'A' : ['apple','potato','bear','broken','arrow'],
							'B' : ['appel','potato','bearz','brokon','arrrow']})
	
	aa['matchPcnt'] = aa.apply(functools.partial(ApplyDF_SM,c1='A',c2='B'), axis=1)

	#####Fuzzy Matches
	#This requires the difflib package
	#
	#Fuzzy matches take a column from one dataframe and try to match it - as best it can - from another dataframe
	#in the below example, df1 has a number and name column, and df2 has a name and letter; a fuzzy match is done on the name and we get the 
	#resulting number and letter match
	#a separate hand-made function is needed here (GetClosestStringMatch) because if there is no match, it will error out; the function is needed
	#to return a NULL /  np.nan if there is no match (and its also useful to remove used matches, if it is desired)
	df1 = pd.DataFrame([[1,'one'],[2,'two'],[3,'three'],[4,'four'],[5,'five'],[6,'six']],columns=['number','name'])
	df2 = pd.DataFrame([['c','three'],['a','one'],['b','too'],['e','five'],['d','fours'],['f','sixxyoo']],columns=['letter','name'])
	
	#I like to have a list of potential matches - JUST MAKE SURE NONE OF THE SELECTIONS ARE NULL OR THE FUNCTION WILL BOMB. 
	#I do this because I like to have the option of removing used items, and this gets dicey if you send the direct series from the dataframe
	myListing = df1['name'].values.tolist()
	
	#actually apply the function. This overwrites the name in df2 with the 'fuzzy match' found in df1 in order for the merge to work
	df2['name'] = df2['name'].apply(lambda x: GetClosestStringMatch(x,myListing))
	
	#merge the two frames - since the 'on' is not specified it uses all matching column names which in this case is 'name'
	df1 = df1.merge(df2,how='left')	
	
	#####other useful functions
	#Transpose (indexes become columns and columns become indexes!)
	aa = dfTest.T
	
	#get basic mathematical stats - count / mean / std / min / 25% (quartile) / 50% / 75% / max as a dataframe; seems to auto-eliminate text fields
	aa = dfTest.describe()
	
	####Regular Expressions
	#Regular expressions can be used with columns
	"""
	A good reference for this is at https://www.tutorialspoint,com/python/python_reg_expressions.htm
	
	Some general notes:
	*Use () instead of [] when grouping
	*It seems every match is replaced, not just the first one
	*It seems that regex replaces are done in real time; for example, if you were replacing ' x ' with 'y'. string ' x x ' would give 'yx '.  
		This is an important distinction
	"""
	aa = pd.DataFrame({'colA' : ['Adams County', 'Adams Co', 'Count Dracula 911', 'e911 Dispatch A', 'e-911 Dispatch B', 'e-9-1-1 Dispatch C', 'County of Adams County and Co', 
								'99 problems but an outage aint 1', "You're gonna have a bad, bad, bad time!", 
								'County Co of Bucks Co', 'Country of Origin', 'Co conspirator Co', 'Montgomery Co.', 'Three spaces:   Two:  One: Space and Tab: 	DONE',
								'This, this is " most * . = - _ ! \' : unacceptable!'],
							'colB' : ['zero', 'one', 'two', 'three','four', 'five', 'six', 'seven', 'eight', 'nine', 'ten', 'eleven', 'twelve', 'thirteen', 'fourteen'],
							'colC' : np.random.randn(15)})		
	bb = aa.copy()
	
	#this replaces ALL instances of 'co' with 'XX' ('co' is case insensitive, set inline)
	bb['colA'] = bb['colA'].str.replace('cO', 'XX', case=False)
	bb = aa.copy()

	#replace all strings of whitespace with a single Z
	bb['colA'] = bb['colA'].str.replace("\s+", 'Z', case=False)
	bb = aa.copy()	

	#This will replace 'co' OR 'co.' ONLY if its an island - so it will replace strings that start with 'co' or 'co.' and followed by a whitespace, 
	#a space followed by co or co. and that is the end of the string, or a co or co. in the middle of the string that is bookended by whitespaces.
	#Note I had to use () - using [] actually means something else for ^ and $ (beginning of string and end of string respectively)
	bb['colA'] = bb['colA'].str.replace("(^|\s)co\.?(\s|$)", 'XX', case=False)
	bb = aa.copy()	
	
	#This does the same as above IN ADDITION TO the word 'county' (so 'country' should stay, as should 'conspirator' and 'count') 
	bb['colA'] = bb['colA'].str.replace("((^|\s)co\.?(\s|$))|((^|\s)county(\s|$))", 'XX', case=False)
	bb = aa.copy()	
	
	#replace all instances of e911 at the beginning, middle, and end of the string
	bb['colA'] = bb['colA'].str.replace("(^|\s)e?\-?9\-?1\-?1(\s|$)", 'XX', case=False)
	bb = aa.copy()	

	#replace any non-alphanumeric / non-whitespace character with nothing
	bb['colA'] = bb['colA'].str.replace("[\!\@\#\$\%\^\&\*\(\)\_\-\+\=\[\{\]\}\\\|\;\:\'\"\<\,\>\.\?\/\`\~]+", '', case=False)
	bb = aa.copy()
	
	#####Word Counts in Columns
	#This is how you can get a unique count of all words in a column in a dataframe
	#note the column can be comprised of entire sentences and this will break it down
	#
	#This uses the 'collections' library (we will use dataframe 'aa' from above) 
	
	#set the wordbank variable as a counter; this is important
	wordbank = collections.Counter()
	
	#this creates the word bank - note the lack of an operator as its done in wordbank.update
	aa['colA'].str.lower().str.split().apply(wordbank.update)
	
	#store to a dataframe and then re-name the columns - we are now done!
	bb = pd.DataFrame.from_dict(wordbank,orient='index').reset_index()
	bb.columns = ['word','count']
	
	###Similarly, if all we wanted to do is capture the unique words we could do this in a similar fashion using set() (collection library not required!)
	wordbank = set()
	aa['colA'].str.lower().str.split().apply(wordbank.update)
	bb = list(wordbank) #saves as a list	
	
	
	#####Get top-n records within a group
	#This is helpful if you wish to 
	aa = pd.DataFrame({'A' : [1, 1, 1, 2, 2, 2, 1, 1, 2, 2, 1, 2], 
					'B' : [10, 40, 20, 
						70, 100, 80,  
						60, 50, 
						120, 110, 
						30, 
						90]})	
	
	#Because the 'preferred' method simply picks the top n rows, we must first do a sort.  See the sorting section for info on this
	#if the order does not matter and you simply need n results for each index / label you can skip the sort
	aa.sort(columns=['B', 'A'], ascending=False, inplace=True)
	
	#this is how you actually perform the operation - use a groupby and then a head
	#it seems counterintuitive as head usually only returns the first n rows encountered, but this actually does it BY GROUP so it works differently 
	#when used in conjunction with a groupby.
	#The reset_index is not necessary, but it will eliminate any multiindex that was created, flattening the result. Depending on the situation - 
	#if the index is already flattened - this may not be needed
	bb = aa.groupby('A').head(3).reset_index(drop=True)
	
	#There is another way too as of Pandas 0.14, but while it looks more intuitive its harder to work with, as it forces a 'series' object to be returned
	#and THEN you must tease out column 'A' (as this creates a series with two indexes, 'A' and then the original index)
	#NOTE THE BELOW DOES NOT WORK because I could not figure out how to effectively tease out all row/index easily - so its harder to get a mapping
	#between 'A' and the value.  Seeing as there is a far more elegant - albeit less intuitive - method available above, just use that instead  
	bb = aa.groupby('A')['B'].nlargest(3)


	print "\n\nBREAK\n\n"
	
	#print bb
	#print "\n\nBREAK\n\n"
	
	difflib.SequenceMatcher(None,"A pple","ApPle").ratio()
	
 def ApplyDF_SM(s,c1,c2):
	return difflib.SequenceMatcher(None,s[c1],s[c2]).ratio()

 def GetClosestStringMatch(myStr,closestList,removeMatched = 0, myCutoff = .6):
	"""
	THIS IS USED FOR FUZZY MATCHING
	
	This function accepts a string, a list that contains potential closes matches to that string, and a cutoff (the cutoff determines how close the string must be;
	.99 is VERY close, .01 is almost not the same string).
	
	Please note that the get_close_matches function does not handle nulls well - so make SURE closestList has no nulls!
	
	CRITICAL: Do NOT pass through a portion of a dataframe to this function - carve out a list from the dataframe instead! This function removes
	elements that are successfully matched IF removeMatched == 1
	"""
	myReturnedList = difflib.get_close_matches(myStr,closestList,n=1,cutoff=myCutoff)
	if (len(myReturnedList) == 0):
		return np.nan
	else:
		retVal = myReturnedList[0]
		if (removeMatched == 1): closestList.remove(retVal)
		return retVal

 if __name__ == "__main__":
	DataFrameTests()

```

