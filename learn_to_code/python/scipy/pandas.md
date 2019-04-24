# Pandas
<!-- clear for public use -->

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

```
One way to manipulate 
def DataFrameTests():   
	
	"""
	#A Note on creating sub-dataframes
	Two ways to copy a dataframe - note that just doing it straight (aa=dfTest OR aa = dfTest[:]) DOES NOT COPY BY VALUE and copies via address
	this means if you change one, you change the other.  To combat this, use the copy() function
	THAT SAID, if we used something like "aa = dfTest[['GMLC_VENDOR', 'FCC_PSAP_ID','PSAP_NAME']]" This DOES seem to make a legit copy of values 
	instead of memory location; also 'aa = dfTest.ix[0:10,0:2]' seems to correctly copy by value as well
	Finally, it seems 'aa = dfTest[0:4]' copies by memory location BUT 'aa = dfTest[0:4].copy()' seems to copy values
	long story short, ALWAYS use copy()
	"""
	
	"""
	#A Note on returning row/column ranges
	If used in the data frame a la dfTest[0:15], it works as in numpy - that is to say, the first 15 rows returned EXCLUDING row 15.
	That said, IF you were to say  dfTest.ix[0:15,0:2] It WOULD print row 15 (which is actually position 16 - so its inclusive - but STRANGELY
	this is still noninclusive of column 2 (so this only prints columns 0 and 1). In short, for rows (first grouping) the last number is included, but for 
	columns the last number is NOT included. This is strange.
	
	In addition, the functions ix (depricated as of pandas 0.20.0, which was released on 2017-05-05), loc, and iloc are almost interchangeable.  
	loc works on labels in an index; iloc works on the positions of the index (so it only takes integers); ix tries to work like loc but falls 
	back to iloc if the label is not found.
	
	ix can be tricky, as if the label IS an integer ix will only use label-based indexing and not use position based; matter of fact it is now 
	depricated 	so it probably should not be used anyway.

	Since the 2017-05-05 release, ix no longer works, so loc or iloc must be used. There is a concept of boolean indexing, which means use a
	boolean expression to return specific rows in a dataframe.  Note that you CANNOT do this with iloc, so you must use loc with this instead 
	(if you try with iloc you will get the error 'iLocation based boolean indexing on an integer type is not available').

	A note about loc - if its used, the value MUST exist; so for example if the index is 'first names' and the list is Sally/Billy/Bob/Ray
	bb.loc['Billy':'Ray',] or bb.loc['Billy':,] should work, but bb.loc['Billy':'John',] will fail as John is not in the index.

	Also note about loc that 'Sally':'Bob' will include Bob as well (whereas, in other circumstances when using :, it does not include the endpoint).

	The rule of thumb is use iloc for positions and loc if you want to specifically reference a noninteger label set OR you are using boolean indexing.

	"""
	
	fileName = '/home/geralt/TEST_GMLC_Comtech.csv'
	dfTest = pd.read_csv(fileName,parse_dates=True)#,index_col="Date"
	
	aa = dfTest[['PSAP_NAME', 'FCC_PSAP_ID']] #this is a way to get just two specific columns. note they will appear in this order

	aa = dfTest[0:15].copy()#save the first 15 rows to aa; this makes a copy so aa has a clean break from dfTest
	aa.iloc[0,1] = 'abc' #Make the cell in row 0, column 1 = 'abc'
	
	####basic splicing
	aa = dfTest.head(2) #save the first two rows
	aa = dfTest.tail(2) #save the last two rows
	aa = dfTest[0:3] #gets the first 3 rows - rows 0, 1, and 2
	#dfTest['20130102':'20130104'] #note that if the indexes were dates, we could do this too.  note the absence of the dash. I AM NOT SURE IF IT MATTERS IF THE DATES ARE SORTED
	bb = aa.iloc[0:10,0:2] #save rows 0 to 10 (inclusive), columns 0 and 1 (but NOT 2). Note that '.copy()' is not required here, but does not hurt 
	bb.iloc[:,1] = 'meep' #change all rows, second column (column = 1) to 'meep'
	#bb.iloc[3,:] = (999, 'Brents County', 'BrentCS')#this saves one specific row - its not valid here as the columns do not align but it will work, see below
	
	###dealing with the column names
	myColNames = dfTest.columns.values #gets all column names
	
	mySpecificColumnNames = dfTest.columns.values[[6,4,1]] #returns specific column names in position 6, 4, and 1, in that order
	mySpecificColumnNames = np.append(mySpecificColumnNames,['INTRADOPSAP_ID']) #this adds a column name to the end - it appends the numpy ndarray	
	print type(mySpecificColumnNames) #good way to get the type of an object
	
	aa = dfTest[mySpecificColumnNames].iloc[:10,:] #store ONLY the desired columns - in a specific order - to bb (and store only the first 10 rows)
	aa.iloc[3,:] = (999, 'Brents County', 'BrentCS', np.nan)#this saves one specific row. note how it saves a null - np.nan
	
	#using booleans to return rows
	aa = dfTest[mySpecificColumnNames].head(10).copy() #get only the first 10 rows
	bb = aa.copy()

	myBolValues = [False, False, True, False, True, False, False, False, False, False]	
	##REMEMBER: this is a BOOLEAN MASK and as such, iloc will NOT work - we must use loc here
	bb = bb.loc[myBolValues,:] #note this only returns positions 2 and 4 (starting from 0) - just as the list of booleans above!
 

	###SQL WHERE like statements (AKA BOOLEAN INDEXING in Pandas)
	"""
	This works as it basically creates a list of booleans (as above), which can be used in a loc function as shown.
	Note that anything that returns a boolean list can be used, but the number of elements MUST match what is in the dataframe calling iloc
	Also note that the entire statement CANNOT be wrapped in parenthesis (), only individual pieces can be; if you are getting errors and the entire 'WHERE'
	statement is wrapped in parenthesis, try taking it out.
	
	There are two separate methods used. The first gives an example on how to target two numbers, a substring, and NULL fields.
	
	The second is similar, yet it uses a list to compare the FCC PSAP IDs. This is more helpful if you need to compare against several values.
	The first attempt in the second method is straightforward but cumbersome. the second attempt is not straightforward - I had to use an internal iloc[] and then 
	had to specify the column again (the isin() function returns ALL columns in the dataframe so you must specify it in the backend iloc call - the FCC PSAP ID just 
	so happened to be the first column). The final attempt is very straightforward and should be used.
	"""
	
	bb = dfTest[mySpecificColumnNames].head(10).copy() #get only the first 10 rows
	
	#first method - no lumping of FCC PSAP IDs
	aa = bb.loc[((bb['FCC_PSAP_ID'] == 1698) | (bb['FCC_PSAP_ID'] == 1841) | bb['PSAP_NAME'].str.contains('Gordon')) & pd.isnull(bb['INTRADOPSAP_ID']),:]
	
	#second method - using multiple FCC PSAP IDs in a list
	#second method, first attempt - hard coded
	aa = bb.loc[((bb.isin({'FCC_PSAP_ID': [1698, 1841]})).iloc[:,0] | bb['PSAP_NAME'].str.contains('Gordon')) & pd.isnull(bb['INTRADOPSAP_ID']),:]
	#second method, second attempt - using a list
	myFCC_IDs = [1698, 1841]
	aa = bb.loc[((bb.isin({'FCC_PSAP_ID': myFCC_IDs})).iloc[:,0] | bb['PSAP_NAME'].str.contains('Gordon')) & pd.isnull(bb['INTRADOPSAP_ID']),:]
	#second method, final attempt - shortened syntax
	aa = bb.loc[(bb['FCC_PSAP_ID'].isin(myFCC_IDs) | bb['PSAP_NAME'].str.contains('Gordon')) & pd.isnull(bb['INTRADOPSAP_ID']),:]

	#also note that you can do entire dataframes without the use of iloc or loc, but the indexes MUST match and the columns must 'make sense' (so no multiplying 
	#strings or any such nonsense).  
	#I dont want to do this live, but here is an example of how it can be done IF the indexes match
	#df[df > 0] = -df
	
	
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

