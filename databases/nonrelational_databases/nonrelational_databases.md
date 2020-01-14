# Non-Relational Databases

# Why Non-Relational Databases?

<font color="green">Non-Relational Databases</font> (also: <font color="green">NoSQL</font>) came into existence when there was a need to support a massive userbase (tens of thousands to even hundreds of millions of users at once). Traditional [relational databases](databases/relational_databases/) have trouble supporting massive amounts of users as its expensive to scale them [vertically](learn_to_code/computer_science_concepts?id=vertical-vs-horizontal-scaling) and its difficult to scale them [horizontally](learn_to_code/computer_science_concepts?id=vertical-vs-horizontal-scaling). It is primairly due this issue of [scaling](databases/nonrelational_databases/nonrelational_databases?id=scaling) that <font color="green">Non-Relational Databases</font> came onto the scene. <font color="green">Non-Relational Databases</font> can horizontally scale fairly easily and can serve millions of customers quickly.

<font color="green">NoSQL</font> / <font color="green">Non-Relational Databases</font> can be a bit of a loose term, as it can reference _any_ non-relational database that does not specifically use SQL. Unlike relational databases, there is not a single type of database in the NoSQL world that follows the same ruleset: there are many different types of NoSQL databases under this umbrella, each with their own strengths and weaknesses.

!> <font color="green">Non-Relational Databases</font> offer scalability and availability over traditional relational databases and compromise on atomicity and / or consistency.

## Meaning of NoSQL

The identifier of a <font color="green">Non-Relational Database</font> is commonly referred to as <font color="green">NoSQL</font>.  <font color="green">NoSQL</font> initially meant 'no SQL', as this userbase wanted something different from SQL, but it is also sometimes referred to as 'not only SQL'.

## Scaling

Databases - particularly those with a multitude of simultaneous connections and transactions (such as social media / content-driven entities) - need to be scaled constantly.

The concept of [vertical scaling vs horizontal scaling](learn_to_code/computer_science_concepts?id=vertical-vs-horizontal-scaling) comes into play here. recall that <font color="purple">Vertical Scaling</font> is when you increase the hardware resources in terms of CPU or RAM, and <font color="purple">Horizontal Scaling</font> is when you introduce more servers to help the load.  

Basically, there are limits on vertical scaling (physical servers can only be expanded so much), and there are problems when trying to scale a [relational database](databases/relational_databases/) horizontally: there needs to be a way to distribute the data across servers and balance the load, which is hard to do horizontally. NoSQL, on the other hand, has the ability to easily scale horizontally. It is primarily due to this ability to scale horizontally that NoSQL became popular.

## Availability

Non-relational databases do not have structure as a relational database would; this means that [tables](databases/relational_databases/database_key_terms?id=table) do not exist and therefore columns do not exist. Under a relational database system, the application using the database would have to be shut down when a column needd to be added to a table; since the data is [semi-structured / unstructured](databases/nonrelational_databases/nosql_database_key_terms?id=structured-unstructured-data) in a non-relational database, there is not a time when the database has to be shut down to add 'columns' to it.

## Schemaless Representation

Relational databases have the concept of a [relational schema](databases/relational_databases/database_key_terms?id=relational-schema), which means the [table](databases/relational_databases/database_key_terms?id=table), its associated columns and data types, and much more are determined in advance; and if new entity (for example, a new column in a table) is required, the application will have to be taken down and the entity must be added. In NoSQL, however, there are no schemas to speak of;  outside of some very basic things, a schema in a NoSQL database is more or less [unstructured](databases/nonrelational_databases/nosql_database_key_terms?id=structured-unstructured-data) (and handled with things like JSON strings, which are highly adaptable).

## Speed

When getting information for an entire row, a NoSQL database is much faster than a traditional relational database in getting the data - the data is at most [semi-structured](databases/nonrelational_databases/nosql_database_key_terms?id=structured-unstructured-data), so there are no JOINS to perform which means less operations to retrieve the data (assuming the data is spread across ; in addition, table / record locking is far less likely to affect the speed of storing / retrieving data as [consistency is not guaranteed in NoSQL](databases/nonrelational_databases/nonrelational_databases?id=acid-out-base-in) as it is in a [relational database](databases/relational_databases/).

## NoSQL Weaknesses

**<font size="4">No Transactions</font>**  

Anything that needs the saftey net of ACID and the guarantee of transactions fully completing or failing should not use NoSQL; included in these are things like online banking or stock exchanges. In addition, if you have a database where it does not make sense to scale horizontally (or there will be no need for it), a relational database may be a better choice.  

**<font size="4">More Application Developer Responsibility</font>**  

Since the data is [semi-structured](databases/nonrelational_databases/nosql_database_key_terms?id=structured-unstructured-data), the database itself is not guaranteed to check for things like, say, entering a string instead of an integer, a float instead of a datetime, or even an entry in a set list. This means that its fully on the _application developer_ to enforce these rules; the developer is responsible for all data integrity and backward compatibility checks, which can be a challenge.  

# ACID Out, BASE In

The concepts of <font color="green">ACID</font> are at the heart of relational databases; these are:

* <font color="purple">Atomicity</font> - Everything in a transaction succeeds or none of it does (there is a rollback on failure).
* <font color="purple">Consistency</font> - A transaction must abide by all rules set forth in things like constraints, cascades, and triggers; if a transaction does not follow the rules it is considered to cannot leave the database in an inconsistent state, which the concept of consistency does not allow.
* <font color="purple">Isolation</font> - Executing concurrent transactions would leave the database in the same state as executing them sequentially.
* <font color="purple">Durability</font> - A completed transaction persists, even after something like a power loss.

By their nature, non-relational databases are spread across a distributed computer system, and the <font color="green">**CAP theorem**</font> (by Eric Brewer) states that a distributed computer system cannot guarantee all of the following three properties at the same time:
* Consistency
* Availability
* Partition tolerance

Unfortunately, the ACID standard will not work for a non-relational database due to this; specicially, <font color="purple">Atomicity</font> and <font color="purple">Consistency</font> cannot be guaranteed, as these concepts cannot be guaranteed over a distributed system. Non-relational databases trade off these properties for scalability, availability, and speed; NoSQL, on the other hand, follows the <font color="green">BASE</font> concept, which is:

* <font color="purple">**B**asic **A**vailability</font> - Indicates that the system does guarantee availability, in terms of the CAP theorem.
* <font color="purple">**S**oft State</font> - The state of the system may change over time, even without input. This is because of the eventual consistency model.
* <font color="purple">**E**ventual Consistency</font> - The database will _eventually_ be consistent, even if there are gaps when it is not.  

Different companies deal with this in different ways; Amazon, for example, may sell the last of an item to multiple people and will rectify it after the fact.  

# NoSQL Databases

Recall that the only stipulation to being a non-relational database is if the rules of the database do not comform to that of a relational database (although I would throw in [they adhere to BASE](databases/nonrelational_databases/nonrelational_databases?id=acid-out-base-in) and [they scale horizontally](databases/nonrelational_databases/nonrelational_databases?id=scaling) as well). There seem to be 5 general approaches to NoSQL currently:
* Column-Oriented  
* Document  
* Graph  
* Key-Value  
* XML  

# Column-Oriented Databases

> I am going to quickly touch on Column-Oriented databases; to read more, see [wikipedia](https://en.wikipedia.org/wiki/Column-oriented_DBMS).  

Traditional relational databases store data by rows: every time new data comes in, a row (or rows) are added to the table.  In contrast, a <font color="green">Column-Oriented Database</font> will store the columns as rows in a table; so for example, if you had a table with 4 columns and 99 rows / entries in a traditional relational database, a <font color="green">Column-Oriented Database</font> will now have 4 'rows' (one for each previous column) and 99 'columns' (representing the old row entity).If you are only interested in one (or a few) columns available in a table, a relational database would return _all_ elements in the row before searching through the specific column, which would mean multiple disk reads. In contrast, a <font color="green">Column-Oriented Database</font> would only return the _specific_ row that represents the column you are searching for, which turns out to be less disk reads on average. In addition, storing records in such a manner naturally lends to indexing each column natively.

The drawback of this is if you consistently request the _entire_ entity (read: relational database version of 'row') of data for one entry; this would mean the <font color="green">Column-Oriented Database</font> would have to continuously look-up the other columns that span the rest of the table, which would mean this approach would be slower than a traditional database.  In practice, however, most searches only look through a fraction of the available rows, so <font color="green">Column-Oriented Databases</font> typically work out better (as far as speed) over relational databases.



## Examples (Column)

Examples of a Column NoSQL Database are:
* Hadoop / HBase  
* BigTable  
* Cassandra  
* Cloudera  
* SompleDB  

# Document Databases

## Examples (Document)

Examples of a Document NoSQL Database are:
* [Couchbase DB](/databases/couchbase/)  
* MongoDB  
* RavenDB  
* Terrastore  

# Graph Databases

## Examples (Graph)

Examples of a Graph NoSQL Database are:
* Neo4J
* FlockDB  
* InfiniteGraph  

# Key-Value Databases

## Key-Value (Document)

Examples of a Key-Value NoSQL Database are:
* Membase  
* Redis  
* Voldemort  
* MemcacheDB  

# XML Databases

## XML (Document)

Examples of an XML NoSQL Database are:
* BaseX  
* eXist  

# Relationships / Foreign Keys

NoSQL databases generally do not support relationships or foreign keys; in order to query across tables, one must execute multiple queries. This is not true of all NoSQL databases, but there is no guarantee of support for foreign keyss or relationships. This can lead to occasionally duplicating data across multiple data sources.
