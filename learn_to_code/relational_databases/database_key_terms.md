# Relational Database Key Terms

# Primary Key
 
A **primary key** is a column – actually often multiple columns – that define a row of data.  So, for example, say you have a table with three columns: employee ID, month, and units sold.  What would define a unique row?  Obviously employee ID helps define a row, so employee ID is a primary key.  Again, we want a unique row identifier, so we have to ask ourselves: can there be multiple rows in the data with the same employee ID?  The answer is yes – since there is also a month column, this indicates an employee can be in this table multiple times, once per month listed.  So, in this example, there are two columns that define a unique row: employee ID and month.
 
It can be difficult figuring out primary keys (unique columns), but here are a few general guidelines (that will identify a primary key in most cases):

* Any column that has the specific word “ID” in it that can be construed as “identification” is probably a primary key (or one of them).
* Any date or date/time columns, IF it’s the only date or date/time column in the table (or if it has one DATE AND one DATETIME column, they are typically both primary key columns).  In the case of multiple date and/or date/time columns you have to use your judgment based on the name of the column.
* Typically, if the column is a column that has numbers in it that are not IDs, it is NOT a primary key.
* First and Last names are typically NOT primary keys.  Why?  Because there are, in theory, multiple people can have the same name.  As an example, there are 24,333 people in the US that have the name “John Smith.”  Do NOT use a first/last name as a primary key, ever – use an employee ID or some other unique identifier.

 
When joining tables, determine the primary keys in each table; then, determine which primary keys from each table should be used to join the tables together.  It is important to identify EVERY primary key pairing between both tables – the more primary keys that you can match the faster the query will execute (as well as eliminate flawed joins).
 
For example, consider two tables:
 
| <font color="purple">Employees</font> | <font color="purple">MonthlyUnitsSold</font> |
| --- | --- |
| EmployeeID | EmployeeID | 
| FirstName | Date | 
| LastName | numOfUnits | 
| Position |  | 
| State |   | 
| MonthlyTargetUnits |        | 

 
For the table Employees, it seems only the EmployeeID identifies a unique row; the MonthlyUnitsSold table uses both the EmployeeID <font color="blue">AND</font> the Date columns to identify a unique row.  What columns do both tables have in common?  That’s right, the EmployeeID is the only column that both tables have in common.  Therefore, if we wanted to join these two tables, we would join them <font color="blue">ON</font> Employees.EmployeeID = MonthlyUnitsSold.EmployeeID. 

# Relational Schema

A **relational schema** (also: **table schema**) describes the [table](learn_to_code/relational_databases/database_key_terms?id=table); it contains things like the name of the table, column names, column data types, etc. 

# Row-level Locking

The need to update specific rows in a [table](learn_to_code/relational_databases/database_key_terms?id=table) is quite common; when **row-level locking** is used, _only_ the affected rows are 'locked' (or'claimed'), which means other entities cannot update the row until the entity that has 'locked' the row is finished and unlocks it. This is opposed to [table-level Locking](learn_to_code/relational_databases/database_key_terms?id=table-level-locking) which locks the _entire_ table instead of just specific rows.

# Schema

A **database schema** (commonly truncated to **schema**) is a collection of [relational schemas](learn_to_code/relational_databases/database_key_terms?id=relational-schema) that are logically connected.

When referenced in a database product (such as MySQL or Oracle or HP Vertica), a schema can mean different things:
* In MySQL, the words 'database' and 'schema' are interchangeable. a schema is a collection of tables (with the schema being represented as a directory in the file system); multiple schemas can exist in a MySQL instance.
* In Oracle (and most other products that are not MySQL or SQLite), a 'schema' is a collection of tables, but a database is a collection of schemas.

# Table

A **table** is simply a collection of data that has both rows and columns; it can be physically present (i.e., exists on a harddrive), but it can also be calculated at query time (which means technically a [view](learn_to_code/relational_databases/database_key_terms?id=view) is also a table).

A table always has some metadata associated with it - in particular, it has a [relational schema](learn_to_code/relational_databases/database_key_terms?id=relational-schema) (not to be confused with a [database schema](learn_to_code/relational_databases/database_key_terms?id=schema)).

# Table-level Locking

The need to update specific rows in a [table](learn_to_code/relational_databases/database_key_terms?id=table) is quite common; when **table-level locking** is used, the _entire_ table is locked (or'claimed'), which means no other entity can update _any_ row in the table until the entity that has 'locked' the table is finished and unlocks it. This is on contrast to [row-level locking](learn_to_code/relational_databases/database_key_terms?id=row-level-locking), which only locks specific rows in the table (and not the entire table), making row-level locking more efficient.

# Transaction

A **transaction** symbolizes a unit of work in a database; it can be thought of as any change in a database. Transactions are 'all or nothing', either they are a full success or they have no effect on the database. A transaction has these properties:
* _Atomic_ - An [atomic transaction](https://en.wikipedia.org/wiki/Atomicity_&#40;database_systems&#41;) is indivisible and irreducible - meaning either the entirety of the transaction happens, or nothing happens. 
 * As an example, think of money being transferred from one bank account to another. The transaction is atomic if and only if the money is fully removed from one account and added to the other; if it fails to be removed from the original account but is added to the target account - or if it is removed from the original account but fails to be added to the target account, this would not be an atomic transaction.
* _Consistent_ - a transaction is [consistent](https://en.wikipedia.org/wiki/Consistency_&#40;database_systems&#41;) only if it follows all of the rules laid out by the constraints laid out in the [relational schema](learn_to_code/relational_databases/database_key_terms?id=relational-schema), cascades, etc.
* _Isolated_ - [isolation](https://en.wikipedia.org/wiki/Isolation_&#40;database_systems&#41;) determines when and how changes become visible to other operations / entities.
* _Durable_ - [Durability](https://en.wikipedia.org/wiki/Durability_&#40;database_systems&#41;) guarantees committed transactions will survive permanently, even if there is a system failure.

For more on transactions, [see here](https://en.wikipedia.org/wiki/Database_transaction).


# View

A **view** can be thought of as a virtual [table](learn_to_code/relational_databases/database_key_terms?id=table). SQL queries can interact with views as it can tables, but the difference is a view does not have physical data on the hard drive: the view is calculated at runtime. 

Views have some advantages:
* They can join multiple tables and have them appear as one table.
* They can calculate columns (SUM, AVG, etc) and have the results appear as if the data was natively in the aggregated format.
* Added security (you can give users access to a view, but not its underlying table - this means sensitive columns can be 'skipped' in the view but still appear in the underlying table.

Realistically, a view is simply a named [query](learn_to_code/relational_databases/sql?id=select-query); in other words, a user can write a query, name it, and then reference that name as they would a regular table.