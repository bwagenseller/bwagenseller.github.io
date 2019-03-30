# General MySQL Maintenance
<!-- clear for public use --> 

# Before We Begin

This document assumes you have already [installed MySQL](ubuntu/package_install/mysql_install) on an [Ubuntu Linux Machine](ubuntu/server_build). If this is not the case, please do so before proceeding.

# Logging Into the Database

!> While you should know how to log in via the terminal, for day to day activities a GUI is suggested. My favorite is [SQLyog](https://github.com/webyog/sqlyog-community) (new download [here](https://github.com/webyog/sqlyog-community/wiki/Downloads), old download [here](https://code.google.com/p/sqlyog/wiki/Downloads)), but [MySQL Workbench](https://www.mysql.com/products/workbench/) (download [here](https://dev.mysql.com/downloads/workbench/)) is also an option.

This is a basic guide to logging into the database.  You can use a program like [SQLyog](https://github.com/webyog/sqlyog-community) or [MySQL Workbench](https://www.mysql.com/products/workbench/) to log in, or you can do it from a terminal.

This will show how to log in through a terminal . In this example you will need to 
* be logged into a terminal on the machine that houses the database
* Assume your MySQL username is 'geralt'
* Assume your password is 'roach'

To log in, type this command: 

> mysql -u geralt -proach -h XXX.XX.XXX.XXX

Note that:
* There is NO space between "-p" and "roach".
* The '-h' flag connects to a remote database with an IP of XXX.XX.XXX.XXX. This is not necessary if the database is located on the local server.

!> WARNING! Some commands must be run from outside the database and some must be run after using the login method above.  If the instructions say you must be 'logged into a terminal', it means the command must be run from outside the database (i.e., from a Linux command line); if the directions say you must 'log into the database' or 'from a MySQL window', this means the method above must be used to log in first.

# Handling Users

## (Re)-Setting the Root Password for MySQL

This sets the root password for MySQL (note: this is different from the root account on Ubuntu itself). This follows the example [here](https://help.ubuntu.com/community/MysqlPasswordReset). Run these commands in Ubuntu: 
> systemctl stop mysql <br>
> ###note: for this command you may see it hang - just press enter after about 30 seconds <br>
> /usr/sbin/mysqld --skip-grant-tables --skip-networking & <br>
> ###note: unless otherwise noted we are in the MySQL> prompt after you run this line) <br>
> mysql -u root <br>
> FLUSH PRIVILEGES; <br>
> SET PASSWORD FOR root@'localhost' = PASSWORD('newPasswordHere'); <br>
> UPDATE mysql.user SET Password=PASSWORD(' newPasswordHere ') WHERE User='root'; <br>
> FLUSH PRIVILEGES; <br>
> ###note: after this command you will be back at the regular UNIX prompt) <br>
> exit <br>
> systemctl stop mysql <br>
> systemctl start mysql <br>

* You will have to replace `newPasswordHere` with your own password above.

The MySQL root password is now either reset or initialized.

## Creating MySQL Users

MySQL user names include their login AND where they are allowed to originate; so for the localhost, root's login is actually 'root'@'localhost'. Conversely, it could also be 'root'@'127.0.0.1'. Its not practical to put in a user for every host, though, so oftentimes the wildcard is used as such: 'root'@'%'.  This sets the root password for MySQL (note: this is different from the root account on Ubuntu itself).

To create a user:
* Log in as root
> mysql -u root -p <br>
* At this point you will be prompted for the password; once entered correctly you will be dumped at the mysql> prompt 
* Set the database (this is not intuitive and seems unnecessary but it must be done, we will just use the default mysql database); type:
> USE mysql;
* Create the user AND their password: 
> CREATE USER 'username'@'%' IDENTIFIED BY 'myPasswordHere!';
* Now rights must be granted to a user.  This example will just give the user all rights on all schemas, but if you want to only give certain rights on certain schemas that can be done too (research the GRANT command for MySQL for more info). 
 * To grant full access: 
> GRANT ALL ON \*.\* TO 'username'@'%' WITH GRANT OPTION;
 * The 'WITH GRANT OPTION' should ONLY be used when you want to give the account the power to give other users power - otherwise omit this.  
 * In fact, be very careful when using ALL in addition to using the global identifier (aka \*.\*); this grants everything to someone globally.
* Flush privileges (to make sure they are pushed to the user): 
> FLUSH PRIVILEGES;
* Exit MySQL
> exit

## Changing a MySQL Password

To change a password, run this command in a MySQL window:

> SET PASSWORD FOR geralt = password('roach');

Notes
* This assumes your username is 'geralt'
* This assumes your new password will be 'roach'

# Checking Thread Statuses

Sometimes its important to know what threads are running on your server.  To see them, run this in a MySQL window:

> SELECT ID, \`user\`, \`host\`, DB, COMMAND, \`TIME\`, (\`TIME\`/60) Minutes, (\`TIME\`/60/60) Hours, State, Info FROM information_schema.PROCESSLIST ORDER BY \`time\` DESC;

# Starting, Stopping, and Statusing the Database

!> Note! To run these commands you must be logged into a terminal on the machine that houses the database; in addition, you must be root.

## Getting a Status on the Database

To get the status of the database, run the following command:

> service mysql status

## Stopping the Database

!> The first thing that must be done is to turn off all scripts that interact with the database, if possible. 

To stop the database, run the following command:

> service mysql stop

## Starting the Database

To start the database, run the following command:

> service mysql start

To check that the database successfully launched, try [logging in](/ubuntu/package_operations/mysql_maintenance?id=logging-into-the-database) to the database.

!> After the database is up and running, all scripts that interact with the database should be turned on if you turned them off. 

# Deleting Schemas

!> WARNING! Do NOT delete a schema without backing it up first or confirming it is no longer necessary, as this will completely remove all data in the schema!  

> A **schema** is a collection of related tables.

Under some circumstances, it is necessary to delete an entire schema - and in extreme cases, all schemas - from a database. When this is required, you will have to log into the database (with an account that has the correct privileges) and run this command:

> DROP SCHEMA SchemaName;

Where 'SchemaName' is the name of the target. This action removes all back-end files that are associated with this schema. If you have to drop all schemas (otherwise known as 'deleting the database'), perform this action for all schemas on the server.

!> If you delete [schemas that you have symlinked](/ubuntu/package_install/mysql_install?id=moving-schemas-in-the-file-system), it will simply delete the symlink and **not** the data in the actual folder. You will have to manually delete the data in the sylinked folder as MySQL cannot do this.  If you do not, you will have unassociated files on your system taking up resources!

# Backup / Restore Data


Its a good idea to occasionally backup an entire schema in MySQL.  The backup saves the data in the entire schema to a flat file; that flat file can be stored anywhere, then loaded again (or to a different server) as necessary. 


## Schema / Table Directory Location

It should be noted that in the back-end, schemas are simply directories and tables are represented by a few files. The schema directories are usually located in `/var/lib/mysql` unless this was changed (it usually is not).

## Backup And Restore Assumptions

In these examples you will need to 
* be logged into a terminal on the machine that houses the database (unless you do this remotely, then see the notes on the -h flag)
* Assume your MySQL username is 'geralt'
* Assume your password is 'roach'
* Assume the schema you are interfacing with is called 'mySchema'
* Assume the file you are interfacing with is called 'mySchema.sql' (or 'dump.sql', depending)

## Automated Database Backup

It is strongly recommended that you write your own script to automate database backups; this due to a few reasons. The largest reason revolves around indexes: when using [mysqldump](ubuntu/package_operations/mysql_maintenance?id=manual-schema-backup), the indexes are left in place. `mysqldump` builds the indexes _first_, _then_ inserts all of the data. Unfortunately its much, much faster to put data in a table _first_, _then_ create the indexes. I highly recommend you write a script to:
1\. Record all indexes, _then_ 
2\. Record all views (I will explain why in a bit), _then_ 
3\. Drop all indexes, _then_
4\. Use [mysqldump](ubuntu/package_operations/mysql_maintenance?id=manual-table-backup) on _indivdual_ tables (I will explain why in a bit), which will give you one .sql file per table (with instructions on how to build the table as well as its contents), _then_
5\. Re-build all indexes.

I highly recommend doing this on a [per table basis](ubuntu/package_operations/mysql_maintenance?id=manual-table-backup) (and NOT backing up [entire schemas all at once](ubuntu/package_operations/mysql_maintenance?id=manual-schema-backup) because this will allow you to [multithread](learn_to_code/python/python_multithreading) the table backup process (one table per thread) which will _dramatically_ speed up the process. I noticed that backing up more than 8-9 tables at once (that is to say, having more than 8 or 9 threads or processes backing up tables at once) led to errors in `mysqldump` during the backup, so you may want to cap max threads / processes at 8 or 9.

I also recommend backing up the views individually. This is because, when backing up individual tables, the views are _not_ captured in `mysqldump` (they _are_ captured in [schema dumps](ubuntu/package_operations/mysql_maintenance?id=manual-schema-backup), but I advise against schema dumps). 

## Manual Schema Backup

!> Under most circumstances, I do not recommend this method - consider the [automatic table backup](ubuntu/package_operations/mysql_maintenance?id=automated-database-backup) method instead!  

Follow these instructions to manually back-up a schema.

In this example you will need to 
* comply with the notes laid out [above](/ubuntu/package_operations/mysql_maintenance?id=backup-and-restore-assumptions)
* have write access to your current directory

Run this command in the terminal:

> mysqldump -u geralt -proach mySchema > mySchema.sql

The file should be saved to your current directory.

Notes
* There is NO space between "-p" and "roach".
* To dump just the structure - WITHOUT data - use the flag -d (it has no value, just send -d).
* If you want to get the data from a foreign IP use the -h flag:  -h XXX.XXX.XXX.XXX

## Manual Table Backup

Follow these instructions to manually back-up a single table.

In this example you will need to 
* comply with the notes laid out [above](/ubuntu/package_operations/mysql_maintenance?id=backup-and-restore-assumptions)
* have write access to your current directory

Run this command in the terminal:

> mysqldump -u geralt -proach mySchemaName myTableName > mySchemaName.myTableName.sql

The file should be saved to your current directory.

Notes
* There is NO space between "-p" and "roach".
* To dump just the structure - WITHOUT data - use the flag -d (it has no value, just send -d).
* If you want to get the data from a foreign IP use the -h flag:  -h XXX.XXX.XXX.XXX

## Automated Database Restore

It is strongly recommended that you write your own script to automate database backups; [see here](ubuntu/package_operations/mysql_maintenance?id=automated-database-backup) for more info. When you wre writing a script to automatically restore, these will be the general steps (again, assuming your backup script [followed these rules](ubuntu/package_operations/mysql_maintenance?id=automated-database-backup)):
1\. Build the schemas manually (this is usually done with a simple `CREATE SCHEMA SchemaNameHERE;`).
2\. Use [mysqldump](ubuntu/package_operations/mysql_maintenance?id=manual-table-restore) to restore your _indivdual_ tables.
3\. Re-build all indexes.
4\. Re-build all views.
 * In some cases, views depend on other views - if they are built out of order they will fail, so you must deal with this somehow.

!> If you restore [schemas that you have symlinked](/ubuntu/package_install/mysql_install?id=moving-schemas-in-the-file-system), make sure you add the symlink back!

## Manual Schema Restore

!> Under most circumstances, I do not recommend this method - consider the [automatic table restore](ubuntu/package_operations/mysql_maintenance?id=automated-database-restore) method instead!  

Follow these instructions to manually restore a schema. This assumes you 
* comply with the notes laid out [above](/ubuntu/package_operations/mysql_maintenance?id=backup-and-restore-assumptions)
* have a backup file, like one performed in [the 'Manual Schema Backup'](/ubuntu/package_operations/mysql_maintenance?id=manual-schema-backup) example above, stored in your current working directory.

Run this command in the terminal:

> mysql -u geralt -proach mySchema < mySchema.sql

Notes
* There is NO space between "-p" and "roach".
* The schema MUST exist before the restore. It cannot be restored without the schema already existing!
* If you want to save the data to a foreign IP use the -h flag:  -h XXX.XXX.XXX.XXX

!> If you restore [schemas that you have symlinked](/ubuntu/package_install/mysql_install?id=moving-schemas-in-the-file-system), make sure you add the symlink back!

## Manual Table Restore

Follow these instructions to manually restore a single table. This assumes you 
* comply with the notes laid out [above](/ubuntu/package_operations/mysql_maintenance?id=backup-and-restore-assumptions)
* have a backup file, like one performed in [the 'Manual Table Backup'](/ubuntu/package_operations/mysql_maintenance?id=manual-table-backup) example above, stored in your current working directory.

Run this command in the terminal:

> mysql -u geralt -proach mySchema < /path/to/table_name.sql

Notes
* There is NO space between "-p" and "roach".
* The schema MUST exist before the restore. It cannot be restored without the schema already existing!
* It is assumed that when you manually backed-up the table, you saved the file to /path/to/table_name.sql
* If you want to save the data to a foreign IP use the -h flag:  -h XXX.XXX.XXX.XXX

!> If you restore [schemas that you have symlinked](/ubuntu/package_install/mysql_install?id=moving-schemas-in-the-file-system), make sure you add the symlink back!

## Manually Backup Entire Database

!> Under most circumstances, I do not recommend this method - consider the [automatic table backup](ubuntu/package_operations/mysql_maintenance?id=automated-database-backup) method instead!  

Sometimes its best to back-up the entire database (all schemas) into one file: this is helpful as if you do it on a per schema basis, it also does the views which can fail as that schema may not exist.

In this example you will need to 
* comply with the notes laid out [above](/ubuntu/package_operations/mysql_maintenance?id=backup-and-restore-assumptions)
* have write access to your current directory

Run this command in the terminal:

> mysqldump -u geralt -proach --all-databases > dump.sql

The file called 'dump.sql' should contain all table builds and data and should be saved to your current directory.

Notes
* There is NO space between "-p" and "roach".
* To dump just the structure - WITHOUT data - use the flag -d (it has no value, just send -d).
* If you want to get the data from a foreign IP use the -h flag:  -h XXX.XXX.XXX.XXX

## Manual Database Restore

!> Under most circumstances, I do not recommend this method - consider the [automatic table restore](ubuntu/package_operations/mysql_maintenance?id=automated-database-restore) method instead!  

Follow these instructions to manually restore the entire database (not just a single schema). This assumes you 
* comply with the notes laid out [above](/ubuntu/package_operations/mysql_maintenance?id=backup-and-restore-assumptions)
* have a backup file, like one performed in [the 'Manually Backup Entire Database'](/ubuntu/package_operations/mysql_maintenance?id=manually-backup-entire-database) example above, stored in your current working directory.
* (Recommended, but may not be required) have [manually deleted](/ubuntu/package_operations/mysql_maintenance?id=deleting-schemas) ALL schemas except for the following
```
information_schema
mysql
performance_schema
sys
```

Run this command in the terminal:

> mysql -u geralt -proach < dump.sql

Notes
* There is NO space between "-p" and "roach".
* If you want to save the data to a foreign IP use the -h flag:  -h XXX.XXX.XXX.XXX

!> If you restore [schemas that you have symlinked](/ubuntu/package_install/mysql_install?id=moving-schemas-in-the-file-system), make sure you add the symlink back!

## General Notes on Backup / Restore


* If you saved views in the backup file, you will get errors if you try to re-load that file when the tables referenced in the views do not exist in the new target server.  There are four options to avoid this:
 * Perform a backup/restore of the entire database (not just individual schemas).
 * Install the schemas in such an order that will complement the views (in other words, if schema A has a view reference to schema B, build schema B first).
 * Remove views before the backup, saving them to a different file (that you can run later).
 * Do not use views (not recommended).
* If the indexes are kept during the backup, the restoral process is far slower as it builds the table first, then the indexes, then populates the data (which means the indexes have to be re-evaluated for every record). To combat this
 * For Backup: store all indexes (that are not primary keys) in a separate file that you can run after the restoral is finished, remove all indexes (that are not primary keys), and then back-up the schema / the entire database
 * For Restoral: restore as normal, then when the restoral is complete run the file that houses the indexes

# Killing a Thread

First, find the process ID of the thread you wish to kill (the 'process ID' field is simply called 'ID'); run this in a SQL window:
> SELECT ID, \`user\`, \`host\`, DB, COMMAND, \`TIME\`, (\`TIME\`/60) Minutes, (\`TIME\`/60/60) Hours, State, Info FROM information_schema.PROCESSLIST WHERE STATE <> '' AND \`time\` > 0 ORDER BY \`time\` DESC;


once you have the number (for example the number is 99999), use this command in a SQL window:
> KILL 99999;
