# MySQL Setup Information
<!-- clear for public use --> 

# MySQL Install

This is how you install MySQL; note that you must be root when performing these actions.

Perform a package update:

> apt-get update

Install the MySQL server:

> apt-get install mysql-server

You will be prompted to supply a password for the root MySQL user. Once the install is complete, MySQL will be up and running. <br>

You will most likely want to also do the following:

* Install [other packages](/ubuntu/package_install/mysql_install?id=other-packages-to-install)
* Set up [mysqld.cnf](/ubuntu/package_install/mysql_install?id=mysql-master-settings-mysqldcnf)
* [Create users](/ubuntu/package_operations/mysql_maintenance?id=creating-mysql-users) and granting them appropriate access
* Consider [disabling strict SQL mode](/ubuntu/package_install/mysql_install?id=disable-strict-sql-mode)


You may also want to install the client, which is

> apt-get install mysql-client

# MySQL Uninstall

This is how you *completely* uninstall MySQL; note that you must be root when performing these actions. <br>

Steps:
* if there is important data on this database [back-up and restore all schemas](/ubuntu/package_operations/mysql_maintenance?id=backup-restore-data) before moving forward.
* Stop MySQL:
> systemctl stop mysql
* Check to make sure its stopped and see if the ports are not active:
> ps -ef | grep -i mysql <br>
> netstat -an | grep 3306 <br>
* Check to see what mysql packages are currently installed:
> apt list --installed | grep mysql
* **Make a note of these and save them**; you may end up removing some other necessary mysql packages and if you re-install mysql you will need all of these. Now remove the mysql packages and then purge them:

!> When purging, you will be prompted if you want to delete the mysql dirs; if you want a FRESH install, answer YES!

> apt-get remove mysql-\* <br>
> apt-get purge mysql-\* <br>

* Now clean obsolete deb-packages:
> apt-get autoclean

# Other Packages to Install

There are other packages that should be installed as well Do not forget to update first:
> apt-get update

 Now install the packages:
> apt-get install libdbd-mysql-perl <br>
> apt-get install mysql-client <br>
> apt-get install libmysqlclient-dev <br>

# (Re)-Setting the Root Password for MySQL

To re-set the root password, please see [these instructions](/ubuntu/package_operations/mysql_maintenance?id=re-setting-the-root-password-for-mysql).

# Creating MySQL Users

To create users, please see [these instructions](/ubuntu/package_operations/mysql_maintenance?id=creating-mysql-users).


# MySQL Master Settings - mysqld.cnf

This is a copy of my favorite settings for MySQL; the file is located at 

> /etc/mysql/mysql.conf.d/mysqld.cnf

Its fine if its owned by root, but it must be globally readable. Note that if you ever want to change this, you will have to [stop the database](/ubuntu/package_operations/mysql_maintenance?id=stopping-the-database), make the changes, and then [start the database](/ubuntu/package_operations/mysql_maintenance?id=starting-the-database).

The contents are:

```
##########
#
# The MySQL database server configuration file.
#
# You can copy this to one of:
# - "/etc/mysql/my.cnf" to set global options,
# - "~/.my.cnf" to set user-specific options.
# 
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# This will be passed to all mysql clients
# It has been reported that passwords should be enclosed with ticks/quotes
# especially if they contain "#" chars...
# Remember to edit /etc/mysql/debian.cnf when changing the socket location.

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

[mysqld_safe]
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
#
# * Basic Settings
#
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /var/lib/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
skip-external-locking
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address		= !!!!!!!!!!THIS_IS_WHERE_YOU_PUT_THE_IP_OF_THE_SERVER!!!!!!!!!!
#
# * Fine Tuning
#
key_buffer_size		= 64G
max_allowed_packet	= 16M
thread_stack		= 192K

thread_cache_size       = 128

# This replaces the startup script and checks MyISAM tables if needed the first time they are touched
myisam-recover-options  = BACKUP

max_connections        = 2000
max_connect_errors     = 100

max_heap_table_size     = 512M
#table_cache            = 64
#thread_concurrency     = 10
#
# * Query Cache Configuration
#

query_cache_limit	= 1024M
query_cache_size        = 0
#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
#general_log_file        = /var/log/mysql/mysql.log
#general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
#log_slow_queries	= /var/log/mysql/mysql-slow.log
#long_query_time = 2
#log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about other settings you may need to change.
#server-id		= 1
#log_bin			= /var/log/mysql/mysql-bin.log
expire_logs_days	= 10
max_binlog_size   = 100M
#binlog_do_db		= include_database_name
#binlog_ignore_db	= include_database_name
#
# * InnoDB
#
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!

innodb_file_per_table = 1
innodb_buffer_pool_size = 32G

innodb_log_file_size = 2047M
innodb_log_buffer_size = 30M
innodb_max_dirty_pages_pct = 90
innodb_open_files = 2048
open_files_limit  = 32768
innodb_flush_log_at_trx_commit = 2
innodb_flush_method = O_DIRECT

#innodb_thread_concurrency = 32

innodb_buffer_pool_size = 76G
#
# * Security Features
#
# Read the manual, too, if you want chroot!
# chroot = /var/lib/mysql/
#
# For generating SSL certificates I recommend the OpenSSL GUI "tinyca".
#
# ssl-ca=/etc/mysql/cacert.pem
# ssl-cert=/etc/mysql/server-cert.pem
# ssl-key=/etc/mysql/server-key.pem

[isamchk]
key_buffer              = 32M
```
* You **must** change the `bind-address` to the IP of the database _or_ you must comment out this variable
* I think `Key_buffer_size` is new but it WAS 16M; a comparable key_buffer WAS 16M in a previous version but I set key_buffer_size to what I set key_buffer to which was 64G
* `thread_cache_size` used to be was 8
*  These variables were not a part of this file initially
 * `max_heap_table_size`
 * `max_connections`
 * `max_connect_errors`
* `query_cache_limit` was 1M and `query_cache_size` was 16M in the initial file
 * [It is recommended](https://www.percona.com/blog/2014/01/28/10-mysql-performance-tuning-settings-after-installation/) to turn `query_cache_size` off altogether (=0) as this is a bottleneck for queries - just use proper indexing
* Many of the INNODB configs taken from [here](http://mysqldump.azundris.com/archives/78-Configuring-InnoDB-An-InnoDB-tutorial.html)
* Leaving `innodb_thread_concurrency` out as there are conflicting reports as to its helpfullness; consider putting this in in the future
* I set `innodb_buffer_pool_size` lower [than suggested here](https://www.percona.com/blog/2014/01/28/10-mysql-performance-tuning-settings-after-installation/), but its still far higher than the default 32G.
* \[isamchk\] `key_buffer` was 16M initially.

!> Note the setting of the bind-address to the host's IP; if you do not want to bind to anything you can comment this out; you can also set this to 127.0.0.1 but know that if you set it to this, you not be able to log in remotely!

# Add Timezone Tables

We want to load zone info as the root user to the mysql table.

To do so, [become root](/ubuntu/linux_notes?id=becoming-root) and execute the following command in a terminal:
```
mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -u root -p mysql
```

If you get a warning about some files Unable to load, don't fret.


# Disable Strict SQL Mode

!> Before you do this, check to see if this is still needed - if you are building a database from the ground up it is probably _not_ needed, as this is only needed if your MySQL tables / indexes / views / etc were built on an ancient version of MySQL. Some of these may solve other problems and its best to leave them in if at all possible!

> The original instructions can be found [here](https://serverpilot.io/docs/how-to-disable-strict-mode-in-mysql-5-7).

If your scripts were written before MySQL 5.7 and cannot run in 5.7 (for example, datetimes that use '0000-00-00 00:00:00'), you should disable strict mode.

To do this, become root and create this file by running this command:

> vi /etc/mysql/conf.d/disable_strict_mode.cnf

Now add the following to that file and then save and close"

> [mysqld] <br>
> sql_mode=IGNORE_SPACE,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

Now re-start MySQL:

> service mysql restart

# Moving Schemas In The File System

Parts of this description were initially found [here](https://stackoverflow.com/questions/4182468/moving-mysql-innodb-database-to-separate-drive).

Sometimes its advantageous to move the physical database files to optimize space on a host server; this can be done using symlinks. To do this, you must understand [how schemas are laid out](/ubuntu/package_operations/mysql_troubleshooting?id=location-of-table-schema-files), and you must know that they either need to reside - or have a symlink - in /var/lib/mysql. <br>

We assume your schema's name / directory is 'SchemaName' and its new home is in /data/mysql2.

First, identify the schema's associated directory in /var/lib/mysql ; then [stop the database](/ubuntu/package_operations/mysql_maintenance?id=stopping-the-database). Once its stopped, copy the directory as a backup and then move it:
> cp -R /var/lib/mysql/SchemaName /data/MYSQLHOLD/ <br>
> mv /var/lib/mysql/SchemaName /data/mysql2/

!> Above, we copied the directory to a new folder first as a backup

Now change the file permissions and ownership
> chown -R mysql:mysql /data/mysql2/SchemaName <br>
> chmod 750 /data/mysql2/SchemaName

Next, make the symlink itself and change its ownership
> ln -s /data/mysql2/SchemaName /var/lib/mysql <br>
> chown -h mysql:mysql /var/lib/mysql/SchemaName <br>

We are almost done, but we still have to make sure this new directory is in AppArmor's MySQL entry (**if** you use AppArmor); to check, vi the file 
> vi /etc/apparmor.d/usr.sbin.mysqld

Look and see if the entry for the new directory (without the schema name) exists; for us there should be at least two lines (not counting the comment):
```
# Allow site linked location
  /data/mysql2/ r,
  /data/mysql2/** rwk,
```

If the above is not present, place it in the file (replacing /data/mysql2 with whatever root directory you are using) and **make sure** that **both** lines end with the comma. Save and quit.

If you added the above lines, check / re-run apparmor with the following command:
> apparmor_parser -r /etc/apparmor.d/usr.sbin.mysqld

If there were no errors, this should return nothing.

Finally, [start the database](/ubuntu/package_operations/mysql_maintenance?id=starting-the-database).
