# Non-Relational Database Key Terms

# Structured / Unstructured Data

<font color="green">Structured Data</font> is when the database (and, at least in theory, those writing applications for the database) knows in advance the data type of all data that will be saved and where it will be saved (integers in some columns, floats in others, dates in others, datetimes in others, etc); in addition, it knows how many data fields will be appended each time a record is stored as well as rulesets for the data (for example, a primary key will mean one column cannot have repeating values, etc).  Relational databases utilize <font color="green">Structured Data</font>.  

<font color="green">Unstructured Data</font> is anthesis to the above; the database does _not_ know the number of columns, their data types, nor the rules governing them.  

NoSQL databases typically lie in a spectrum that range from <font color="green">Unstructured Data</font> to 'a decent amount of structure, but not completely known' (which is known as <font color="green">Semi-snstructured Data</font>).  

Practically speaking, <font color="green">Unstructured Data</font> is usually stored in something like a JSON or XML format.  