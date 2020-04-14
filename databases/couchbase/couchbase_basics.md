# Couchbase Basics

# Why Couchbase



# Non-Relational Databases

Couchbase is known as a <font color="green">Non-Relational Database</font> (also known as a <font color="green">NoSQL Database</font>), which is in contrast to a [relational database](databases/relational_databases/). In a <font color="green">Non-Relational Database</font>, 

# Document Expiration

Each document has an expiration time (which has a unit of seconds); that said, setting this time is a bit odd. If you set the expiration to expire 30 days (`60*60*24*30` seconds) or less, you can use an offset (so setting expiration to 60 would mean the document would delete 60 seconds after you upload it); **HOWEVER**, anything _more than_ that time is considered an absolute date (i.e. seconds since the epoch). I suggest _always_ using seconds since the epoch to avoid any problems.