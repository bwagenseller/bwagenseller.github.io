# General MySQL Troubleshooting
<!-- clear for public use --> 

# Basic Database Troubleshooting

!> Note! To run these commands you must be logged into the database itself.  These must be run in a MySQL window.

## View Table Locks

To view all table lock status, run this command while logged into the database:

> SHOW OPEN TABLES; 

## Unlocking Tables

Under normal circumstances, tables can be locked for various reasons; sometimes, these locks are not released as they should be.  When this happens you will need to unlock these tables.

!> You can view the locks whenever you want, but when forcing an unlock of tables make SURE no one else is operating in the database; they could potentially try another action that needs a lock, and their action could render the specific table permanently inoperable.  Make SURE everyone else is out of the database!

To unlock all tables, log into the database (with an account that has the correct privileges) and run this command:

> UNLOCK TABLES; 

## Flushing Caches

Sometimes database performance is hindered by cached data, and it is necessary to flush data from various caches.  To do so, log into the database (with an account that has the correct privileges) and run these commands (in this order):

```
FLUSH LOGS; #just dumps logs
RESET MASTER; #resets logs
FLUSH QUERY CACHE;
FLUSH TABLES;
```

## Viewing the Error Log

Occasionally its a good idea to view the MySQL error log.  It is located in this file on the server:

> /var/log/mysql/error.log

Sometimes its beneficial to see the last 100 lines in the error log; to do so, run this in a terminal:

> tail -100 /var/log/mysql/error.log


# INNODB Transaction Problems

Sometimes, you will [investigate the log file](/databases/mysql/mysql_troubleshooting?id=viewing-the-error-log) and find that there are INNODB transaction problems (in other words, some transaction continuously fails, which may actually halt the start of the entire database).  If this happens, you will have to [back-up the data](/databases/mysql/mysql_maintenance?id=backup-restore-data) and then [delete all schemas](/databases/mysql/mysql_maintenance?id=deleting-schemas).

There are 3 files associated with INNODB, and they are all in the directory /var/lib/mysql:
* ibdata1 - ALL transactions are captured here and will remain here pretty much forever, even after you delete the associated table; as a consequence this file can get huge (gigs in size), and if there is a bad transaction in here it will corrupt the database indefinitely. Unfortunately, there is no way to shrink - or otherwise clear - this file without some drastic measures: delete ALL schemas, stop the DB, and then manually delete this file. 
* ib_logfile0 & ib_logfile1: These are the INNODB buffers.  If you have any problems, you can stop the DB and delete these files and it may be fixed.  Be warned that this may cause stability issues.

## Recover from INNODB Transaction Problems


Here are the steps to take to recover from INNODB transaction problems:

* [Back-up the entire database](/databases/mysql/mysql_maintenance?id=backup-restore-data) 
 * If there are problems with starting the database, you may need to follow the instructions for [starting the database when it fails to start due to an INNODB transaction issue](/databases/mysql/mysql_troubleshooting?id=database-fails-to-start-innodb).
* Back-up the INNODB tables in the 'mysql' schema
 * To find out which tables these are, run this command in any SQL window:
> SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA='mysql' AND ENGINE='InnoDB';
 * As of MySQL 5.7, these are the INNODB tables that exist in the MySQL schema that you will have to manually back-up (use 'mysql' as the schema name):
> engine_cost <br>
> gtid_executed <br>
> help_category <br>
> help_keyword <br>
> help_relation <br>
> help_topic <br>
> innodb_index_stats <br>
> innodb_table_stats <br>
> plugin <br>
> server_cost <br>
> servers <br>
> slave_master_info <br>
> slave_relay_log_info <br>
> slave_worker_info <br>
> time_zone <br>
> time_zone_leap_second <br>
> time_zone_name <br>
> time_zone_transition <br>
> time_zone_transition_type
* [Delete all schemas](/databases/mysql/mysql_maintenance?id=deleting-schemas).
* [Stop the database](/databases/mysql/mysql_maintenance?id=stopping-the-database).
* Back-up (and move, at the same time) the file ibdata1:
 * Note you must do this in a terminal and you must be the root user
> mv /var/lib/mysql/ibdata1 /var/lib/mysql/ibdata1.BAK
 * This file will automatically be re-created by MySQL upon a database re-start.
* Back-up (and move, at the same time) the files ib_logfile0 & ib_logfile1:
 * Note you must do this in a terminal and you must be the root user
> mv /var/lib/mysql/ib_logfile0 /var/lib/mysql/ib_logfile0.BAK <br> 
> mv /var/lib/mysql/ib_logfile1 /var/lib/mysql/ib_logfile1.BAK
 * These files will automatically be re-created by MySQL upon a database re-start.
* Remove the associated files with the INNODB tables in the 'mysql' schema.
 * These should be the *.ibd and *.frm files - one of these for each INNODB table found by searching for all INNODB tables in the 'mysql' schema above - in the folder /var/lib/mysql/mysql
 * For a quick reference, run the following commands to simply back-up the files, which is similar to deleting them (note these may change if the table listing above changes):
> mv /var/lib/mysql/mysql/engine_cost.ibd /var/lib/mysql/mysqlengine_cost.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/gtid_executed.ibd /var/lib/mysql/mysqlgtid_executed.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/help_category.ibd /var/lib/mysql/mysqlhelp_category.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/help_keyword.ibd /var/lib/mysql/mysqlhelp_keyword.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/help_relation.ibd /var/lib/mysql/mysqlhelp_relation.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/help_topic.ibd /var/lib/mysql/mysqlhelp_topic.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/innodb_index_stats.ibd /var/lib/mysql/mysqlinnodb_index_stats.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/innodb_table_stats.ibd /var/lib/mysql/mysqlinnodb_table_stats.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/PLUGIN.ibd /var/lib/mysql/mysqlPLUGIN.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/server_cost.ibd /var/lib/mysql/mysqlserver_cost.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/servers.ibd /var/lib/mysql/mysqlservers.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/slave_master_info.ibd /var/lib/mysql/mysqlslave_master_info.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/slave_relay_log_info.ibd /var/lib/mysql/mysqlslave_relay_log_info.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/slave_worker_info.ibd /var/lib/mysql/mysqlslave_worker_info.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone.ibd /var/lib/mysql/mysqltime_zone.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone_leap_second.ibd /var/lib/mysql/mysqltime_zone_leap_second.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone_name.ibd /var/lib/mysql/mysqltime_zone_name.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone_transition.ibd /var/lib/mysql/mysqltime_zone_transition.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone_transition_type.ibd /var/lib/mysql/mysqltime_zone_transition_type.ibd.BAK; <br>
> mv /var/lib/mysql/mysql/engine_cost.frm /var/lib/mysql/mysqlengine_cost.frm.BAK; <br>
> mv /var/lib/mysql/mysql/gtid_executed.frm /var/lib/mysql/mysqlgtid_executed.frm.BAK; <br>
> mv /var/lib/mysql/mysql/help_category.frm /var/lib/mysql/mysqlhelp_category.frm.BAK; <br>
> mv /var/lib/mysql/mysql/help_keyword.frm /var/lib/mysql/mysqlhelp_keyword.frm.BAK; <br>
> mv /var/lib/mysql/mysql/help_relation.frm /var/lib/mysql/mysqlhelp_relation.frm.BAK; <br>
> mv /var/lib/mysql/mysql/help_topic.frm /var/lib/mysql/mysqlhelp_topic.frm.BAK; <br>
> mv /var/lib/mysql/mysql/innodb_index_stats.frm /var/lib/mysql/mysqlinnodb_index_stats.frm.BAK; <br>
> mv /var/lib/mysql/mysql/innodb_table_stats.frm /var/lib/mysql/mysqlinnodb_table_stats.frm.BAK; <br>
> mv /var/lib/mysql/mysql/PLUGIN.frm /var/lib/mysql/mysqlPLUGIN.frm.BAK; <br>
> mv /var/lib/mysql/mysql/server_cost.frm /var/lib/mysql/mysqlserver_cost.frm.BAK; <br>
> mv /var/lib/mysql/mysql/servers.frm /var/lib/mysql/mysqlservers.frm.BAK; <br>
> mv /var/lib/mysql/mysql/slave_master_info.frm /var/lib/mysql/mysqlslave_master_info.frm.BAK; <br>
> mv /var/lib/mysql/mysql/slave_relay_log_info.frm /var/lib/mysql/mysqlslave_relay_log_info.frm.BAK; <br>
> mv /var/lib/mysql/mysql/slave_worker_info.frm /var/lib/mysql/mysqlslave_worker_info.frm.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone.frm /var/lib/mysql/mysqltime_zone.frm.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone_leap_second.frm /var/lib/mysql/mysqltime_zone_leap_second.frm.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone_name.frm /var/lib/mysql/mysqltime_zone_name.frm.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone_transition.frm /var/lib/mysql/mysqltime_zone_transition.frm.BAK; <br>
> mv /var/lib/mysql/mysql/time_zone_transition_type.frm /var/lib/mysql/mysqltime_zone_transition_type.frm.BAK; <br>
* IF you have set innodb_force_recovery in /etc/mysql/my.cnf, remove that setting now.
* [Start the database](/databases/mysql/mysql_maintenance?id=starting-the-database).
* [Restore the entire database](/databases/mysql/mysql_maintenance?id=backup-restore-data).
* [Manually restore the INNODB tables](/databases/mysql/mysql_maintenance?id=manual-table-restore) in the 'mysql' schema.

!> The documentation for saving / deleting the 'mysql' INNODB tables on the [official page, under the heading 'Decreasing the Size of the InnoDB System Tablespace'](https://dev.mysql.com/doc/refman/5.7/en/innodb-resize-system-tablespace.html) is confusing and may not be correct; it may give you errors to the point where you can no longer log in and you may end up having to [uninstall](/databases/mysql/mysql_install?id=mysql-uninstall) and then [reinstall](/databases/mysql/mysql_install?id=mysql-install) MySQL!

# Database Fails to Start - INNODB

In some cases the database won't even start, and its due to an INNODB issue; if this is the case you will have to set innodb_force_recovery in the file /etc/mysql/my.cnf. [The original notes are located here](https://dev.mysql.com/doc/refman/5.7/en/forcing-innodb-recovery.html). 

If this is the case, your best bet is to try to get the database operational and immediately 

[back-up all schemas](/databases/mysql/mysql_maintenance?id=backup-restore-data); you may even be required to [delete all schemas](/databases/mysql/mysql_maintenance?id=deleting-schemas) and then [restore the database](/databases/mysql/mysql_maintenance?id=backup-restore-data).

To attempt to get the database operational, you will have to set innodb_force_recovery in the file /etc/mysql/my.cnf. There are 0-6 levels; each having increasing consequences. 0 is the default (and is assumed if this setting is missing). When troubleshooting, try setting this to 1 first, then 2, then 3, and so on; after each attempt, try to [start the database](/databases/mysql/mysql_maintenance?id=starting-the-database) and see if it came on (attempt to connect to the database to check).  Any value after 3 may actually the damage the DB, so ONLY go up by 1 incrementally and REALLY consider the consequences of going from 3 to 4.  The levels are described [here](https://dev.mysql.com/doc/refman/5.7/en/forcing-innodb-recovery.html). 

And example of this in the file /etc/mysql/my.cnf under the \[mysqld\] section:

> \[mysqld\] <br>
> innodb_force_recovery = 1

!> Note that after you set this and take any further action (including backing up, deleting all schemas, and then restoring the database), make sure to remove the innodb_force_recovery entry from /etc/mysql/my.cnf before resuming normal database operations!

# Location of Table / Schema Files

The database saves the tables as files and the schemas as their directories.  The schemas are represented as directories in the following directory:

> /var/lib/mysql

In those directories are the associated files of each table.
