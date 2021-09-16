# Memo

+ JBoss standalone : No EJB. Need standalone full

# Pres

## Coding principles

+ [Are You a SOLID Coder?](http://www.infoq.com/presentations/solid-principles)

## JVM

+ [A JVM does what ???](https://www.parleys.com/tutorial/a-jvm-does-what)

## Scala

+ [Why Scala?](https://www.youtube.com/watch?v=LH75sJAR0hc)

## Java

+ [Java 8 in angar](https://www.parleys.com/tutorial/java-8-anger-1)

## Social

+ [The Problem With Women: A Technical Approach](https://www.parleys.com/tutorial/the-problem-with-women-a-technical-approach)

## Git

+ [Knowledge is Power: Getting out of trouble by understanding Git](https://www.youtube.com/watch?v=sevc6668cQ0)

# Docker

The directory is the context to build the image

* List images : docker images
* List all containers : docker ps -a
* Load an image into the container : docker run imageName. The RUN command just run during docker build phase
* docker run -i -t imageName /bin/bash : **i**nteractive with a **t**ty
* docker run -d -P name : -**d**etached -P for exposed ports
* docker run with volume : docker run **-v /host/file:/container/file** name command
* Get ip : docker inspect -f '{{ .NetworkSettings.IPAddress }}'
* Shell into running container : docker exec -it name bash
* build image : docker build -t name .
* Delete all containers : docker rm $(docker ps -a -q)
* Change storage option : In */etc/sysconfig/docker-storage* : DOCKER_STORAGE_OPTIONS= --storage-opt dm.basesize=20G


## Data volumes

It's a directory within one or more containers that bypasses the Union File System. They provide :

* Volumes are initialized when a container is created. If the container’s base image contains data at the specified mount point, that existing data is copied into the new volume upon volume initialization.
* Data volumes can be shared and reused among containers.
* Changes to a data volume are made directly.
* Changes to a data volume will not be included when you update an image.
* Data volumes persist even if the container itself is deleted.

Data volumes are designed to persist data, independent of the container’s life cycle. Docker therefore never automatically delete volumes when you remove a container, nor will it “garbage collect” volumes that are no longer referenced by a container.

### Adding a data volume

You can add a data volume to a container using the -v flag with the docker create and docker run command. You can use the -v multiple times to mount multiple data volumes.

> $ docker run -it -**v** /webapp training/webapp mysql bash

This will create a new volume inside a container at */webapp*

### Locating a volume

You can locate the volume on the host by utilizing the ‘docker inspect’ command.

> $ docker inspect web

### Mount a host directory as a data volume

In addition to creating a volume using the -v flag you can also mount a directory from your Docker daemon’s host into a container.

> $ docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py

This will mount the host directory, /src/webapp, into the container at /opt/webapp.

# Grails

If stuck a *starting application*, in Config.groovy : 

~~~groovy
log4j.main = {
    error  'org.codehaus.groovy.grails.web.servlet', // change error to all     
    all  'org.codehaus.groovy.grails.web.servlet',        
...
}
~~~


# Go

## Slices and Arrays

### Arrays

An array type definition specifies a **length** and an **element type**. An array's size is fixed; its length is part of its type ([4]int and [5]int are distinct, incompatible types).

~~~go
var a [4]int
a[0] = 1
i := a[0]
// i == 1
~~~

Arrays do not need to be initialized explicitly; the zero value of an array is a ready-to-use array

The in-memory representation of [4]int is just four integer values laid out sequentially:

Go's arrays are values. An array variable denotes the entire array; it is not a pointer to the first array element (as would be the case in C). This means that when you assign or pass around an array value you will make a copy of its contents. (To avoid the copy you could pass a pointer to the array, but then that's a pointer to an array, not an array.) One way to think about arrays is as a sort of struct but with indexed rather than named fields: a fixed-size composite value.

An array literal can be specified like so:

b := [2]string{"Penn", "Teller"}
Or, you can have the compiler count the array elements for you:

b := [...]string{"Penn", "Teller"}
In both cases, the type of b is [2]string.

### Slices

The type specification for a slice is []T, where T is the type of the elements of the slice. Unlike an array type, a **slice type has no specified length.**

A slice literal is declared just like an array literal, except you leave out the element count:

letters := []string{"a", "b", "c", "d"}

A slice can be created with the built-in function called make, which has the signature,

*func make([]Type, length, capacity) []T*

~~~go
var s []byte
s = make([]byte, 5, 5)
// or s := make([]byte, 5)
// s == []byte{0, 0, 0, 0, 0}
~~~

# Typical memory usage

Type | Bytes
-----|------
boolean | 1
byte | 1
char | 2
int | 4
float | 4
long | 8
double | 8
|
char[] | 2N + 24
int[] | 4N + 24
|
Object | 16
Reference | 8
Padding | Multiple of 8 bytes

# Union Find

## Quick Find - Eager approach

Begin with id[], if two given objects have the same id, they are connected

| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 
|---|---|---|---|---|---|---|---|---|---
| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 

union(4, 3)

| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 
|---|---|---|---|---|---|---|---|---|---
| 0 | 1 | 2 | 3 | **3** | 5 | 6 | 7 | 8 | 9 

union(3, 8)

| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 
|---|---|---|---|---|---|---|---|---|---
| 0 | 1 | 2 | **8** | **8** | 5 | 6 | 7 | 8 | 9 

etc..

So there can ben a lot of changes. Checking if two elements are connected is very fast but building the array is very slow. N union on N objects is quadratic

## Quick union - Lazy approach

~~~~
0-1-2-3-4-5-6-7-8-9 
~~~~

union(4, 3)

~~~~
0-1-2-3-5-6-7-8-9 
      |
      4
~~~~

union(3, 8)

~~~~
0-1-2-5-6-7-8-9 
            |
            3
            |
            8
~~~~

union(9, 4), union(6, 5)

~~~~
0-1-2-5-7-8 
      |   |\
      6   3 9
          |
          4
~~~~

union(9, 4), union(2, 1), union(6, 5)

~~~~
0-1-5-7-8 
  | |   |\
  2 6   3 9
        |
        4
~~~~

union(5, 0), union(7, 2), union(6, 1)

~~~~
  1 - 8 
 /|\  |\
0 2 7 3 9
|     |
5     4
|       
6       
~~~~
Now building the tree is quick but finding if two elements are connected can be quite slow (Can go up to N)

## Quick Union - Weighted 

The difference here is that we link to root of the smaller tree to the root of the bigger tree. The goal is to avoid getting tall trees

So instead of getting 

~~~~
0 - 1 - 8 
|   |\  |\
5   2 7 3 9
|       |
6       4              
~~~~

union(6, 1)

~~~~
  1 - 8 
 /|\  |\
0 2 7 3 9
|     |
5     4
|       
6       
~~~~

We get : 

~~~~
0  -  8 
|\    |\
5 1   3 9
| |\  |
6 2 7 4        
~~~~


# Order of growth 

|   Order   |   Name     |Solvable in 1990s | Solvable in 2000s    |
|-----------|------------|-----------|------------|
|         1 |   Constant |    any    | any |
|     log N | Logarithmic|any | any |
|         N |     Linear |Hundreds of millions | billions |
|   N log N |linearithmic| millions | hundreds of millions |
|         N²|  quadratic | thousands | tens of thousands |
|         N³|      cubic | thousand | thousands |
|       2^N |exponential | 20s | 30s |

# MySQL

## max_connections

The maximum number of connections MySQL can support depends on the quality of the thread library on a given platform, the amount of RAM available, how much RAM is used for each connection, the workload from each connection, and the desired response time. Linux or Solaris should be able to support at 500 to 1000 simultaneous connections routinely and as many as 10,000 connections if you have many gigabytes of RAM available and the workload from each is low or the response time target undemanding.

## Backup

mysqldump -uuser -ppaswword **--all-databases** > /path/file
mysqldump: Got error: 1286: Unknown table engine 'PERFORMANCE_SCHEMA' when using LOCK TABLES => *rm -rf /var/lib/mysql/ib_logfile*. *service mysqld restart*

## ERROR 1206 (HY000): The total number of locks exceeds the lock table size

It doesn’t have enough room to store all of the row locks that it would need to execute your query. 

### Fixes

* Adjust innodb_buffer_pool_size and restart MySQL. By default, this is set to only 8MB.
* Limit the number of rows manipulated by the query

## MySQL replication

One of the difficulties with a large and active MySQL database is making clean backups without having to bring the server down. Otherwise, a backup may slow down the system and there may be inconsistency with data, since related tables may be changed while another is being backed up. Taking the server down will ensure consistency of data, but it means interruption of service to users. Sometimes this is necessary and unavoidable, but daily server outages for backing up data may be unacceptable. A simple alternative method to ensure reliable backups without having to shut down the server daily is to set up replication for MySQL.

Typically, replication is a system configuration whereby the MySQL server, known in this context as a master server, houses the data and handles client requests, while another MySQL server (a slave server) contains a complete copy of the data and duplicates all SQL statements in which data is changed on the master server right after it happens. There are several uses for replication (e.g., load balancing), but the concern of this article has to do with using replication for data backups. You can set up a separate server to be a slave and then once a day turn replication off to make a clean backup. When you're done, replication can be restarted and the slave will automatically query the master for the changes to the data that it missed while it was offline. Replication is an excellent feature and it's part of MySQL. You just need to set it up.

### The Replication Process

Before explaining how to set up replication, let me quickly explain the steps that MySQL goes through to maintain a replicated server. The process is different depending on the version of MySQL. For purposes of this article, my comments will be for version 4.0 or higher, since most systems now are using the later versions.

When replication is running, basically, as SQL statements are executed on the master server, MySQL records them in a binary log (bin.log) along with a log position identification number. The slave server in turn, through an IO thread, regularly and very often reads the master's binary log for any changes. If it finds a change, it copies the new statements to its relay log (relay.log). It then records the new position identification number in a file (master.info) on the slave server. The slave then goes back to checking the master binary log, using the same IO thread. When the slave server detects a change to its relay log, through an SQL thread the slave executes the new SQL statement recorded in the relay log. As a safeguard, the slave also queries the master server through the SQL thread to compare its data with the master's data. If the comparison shows inconsistency, the replication process is stopped and an error message is recorded in the slave's error log (error.log). If the results of the query match, the new log position identification number is recorded in a file on the slave (relay-log.info) and the slave waits for another change to the relay log file.

This process may seem involved and complicated at first glance, but it all occurs quickly, it isn't a significant drain on the master server, and it ensures reliable replication. Also, it's surprisingly easy to set up. It only requires a few lines of options to be added to the configuration file (i.e., my.cnf) on the master and slave servers. If you're dealing with a new server, you'll need to copy the databases on the master server to the slave to get it caught up. Then it's merely a matter of starting the slave for it to begin replicating.

### The Replication User

There are only a few steps to setting up replication. The first step is to set up a user account to use only for replication. It's best not to use an existing account for security reasons. To do this, enter an SQL statement like the following on the master server, logged in as root or a user that has GRANT OPTION privileges:

~~~~sql
GRANT REPLICATION SLAVE, REPLICATION CLIENT
    ON *.*
    TO 'replicant'@'slave_host'
    IDENTIFIED BY 'my_pwd';
~~~~

In this SQL statement, the user account replicant is granted only what's needed for replication. The user name can be almost anything. The host name (or IP address) is given in quotes. You should enter this same statement on the slave server with the same user name and password, but with the master's host name or IP address. This way, if the master fails and will be down for a while, you could redirect users to the slave with DNS or by some other method. When the master is back up, you can then use replication to get it up to date by temporarily making it a slave to the former slave server. Incidentally, if you upgraded MySQL to version 4.0 recently, but didn't upgrade your mysql database, the GRANT statement above won't work because these privileges didn't exist in the earlier versions. For information on fixing this problem, see MySQL's documentation on Upgrading the Grants Tables.

### Configuring the Servers

Once the replication user is set up on both servers, we will need to add some lines to the MySQL configuration file on the master and on the slave server. Depending on the type of operating system, the file will probably be called my.cnf or my.ini. On Unix-type systems, the configuration file is usually located in the /etc directory. On Windows systems, it's usually located in c:\ or in c:\Windows. Using a text editor, add the following lines to the configuration file, under the [mysqld] group heading:

~~~~sql
server-id = 1
log-bin = /var/log/mysql/bin.log
~~~~

The server identification number is an arbitrary number to identify the master server. Almost any whole number is fine. A different one should be assigned to the slave server to keep them straight. The second line above instructs MySQL to perform binary logging to the path and file given. The actual path and file name is mostly up to you. Just be sure that the directory exists and the user mysql is the owner, or at least has permission to write to the directory. Also, for the file name use the suffix of ".log" as shown here. It will be replaced automatically with an index number (e.g., ".000001") as new log files are created when the server is restarted or the logs are flushed.

For the slave server, we will need to add a few more lines to the configuration file. We'll have to provide information on connecting to the master server, as well as more log file options. We would add lines similar to the following to the slave's configuration file: 

~~~~sql
server-id = 2

master-host = mastersite.com
master-port = 3306
master-user = replicant
master-password = my_pwd

log-bin = /var/log/mysql/bin.log
log-bin-index = /var/log/mysql/log-bin.index
log-error = /var/log/mysql/error.log

relay-log = /var/log/mysql/relay.log
relay-log-info-file = /var/log/mysql/relay-log.info
relay-log-index = /var/log/mysql/relay-log.index
~~~~

This may seem like a lot, but it's pretty straightforward once you pick it apart. The first line is the identification number for the slave server. If you set up more than one slave server, give them each a different number. If you're only using replication for backing up your data, though, you probably won't need more than one slave server. The next set of lines provides information on the master server: the host name as shown here, or the IP address of the master may be given. Next, the port to use is given. Port 3306 is the default port for MySQL, but another could be used for performance or security considerations. The next two lines provide the user name and password for logging into the master server.

The last two stanzas above set up logging. The second to last stanza starts binary logging as we did on the master server, but this time on the slave. This is the log that can be used to allow the master and the slave to reverse roles, as mentioned earlier. The binary log index file (log-bin.index) is for recording the name of the current binary log file to use. As the server is restarted or the logs are flushed, the current log file changes and its name is recorded here. The log-error option establishes an error log. If you don't already have this set up, you should, since it's where any problems with replication will be recorded. The last stanza establishes the relay log and related files mentioned earlier. The relay log makes a copy of each entry in the master server's binary log for performance's sake, the relay-log-info-file option names the file where the slave's position in the master's binary log will be noted, and the relay log index file is for keeping track of the name of the current relay log file to use for replicating.

### Copying Databases and Starting Replication

If you're setting up a new master server that doesn't contain data, then there's nothing left to do but restart the slave server. However, if you're setting up replication with an existing server that already has data on it, you will need to make an initial backup of the databases and copy it to the slave server. There are many methods to do this; for our examples, we'll use the utility mysqldump to make a backup while the server is running. However, there's still the problem with attaining consistency of data on an active server. Considering the fact that once you set up replication you may never have to shut down your server for backups again, it might be worth while at least to lock the users out this one last time to get a clean, consistent backup. To run the master server so that only root has access, we can reset the variable max_connections like so:

~~~~sql
SHOW VARIABLES LIKE 'max_connections';

+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 100   |
+-----------------+-------+

SET GLOBAL max_connections = 0;
~~~~

The first SQL statement isn't necessary, but we may want to know the initial value of the max_connections variable so that we can change it back when the backup is finished. Although setting the variable to a value of 0 suggests that no connections are allowed, one connection is actually reserved for the root user. Of course, this will only prevent any new connections. To see if there are any connections still running, enter SHOW PROCESSLIST;. To terminate any active processes, you can use the KILL statement.

With exclusive access to the server, using mysqldump is usually very quick. We would enter the following from the command line on the master server:

~~~~sql
mysqldump --user=root --password=my_pwd \
      --extended-insert --all-databases \
      --master-data  > /tmp/backup.sql  
~~~~

This will create a text file containing SQL statements to create all of the databases and tables with data. The --extended-insert option will create multiple-row INSERT statements and thereby allow the backup to run faster, for the least amount of down time or drain on services. The --master-data option above locks all of the tables during the dump to prevent data from being changed, but allows users to continue reading the tables. With exclusive access, this feature isn't necessary. However, this option also adds a few lines like the following to the end of the dump file:

~~~~sql
--
-- Position to start replication from
--

CHANGE MASTER TO MASTER_LOG_FILE='bin.000846' ;
CHANGE MASTER TO MASTER_LOG_POS=427 ;
~~~~

When the dump file is executed on the slave server, these last lines will record the name of the master's binary log file and the position in the log at the time of the backup, while the tables were locked. When replication is started, it will go to this log file and execute any SQL statements recorded starting from the position given. This is meant to ensure that any data changed while setting up the slave server isn't missed. To execute the dump file to set up the databases and data on the slave server, copy the dump file to the slave server, make sure MySQL is running, then enter something like the following on the slave:

> mysql --user=root --password=my_pwd < /tmp/backup.sql

This will execute all of the SQL statements in the dump file, which will include the CREATE and INSERT statements. Once the backed-up databases are loaded onto the slave server, execute the following SQL statement while logged in as root on the slave:

> START SLAVE;

After this statement is run, the slave will connect to the master and get the changes it missed since the backup. From there, it will stay current by continuously checking the binary log as outlined before.

## Backups with Replication

With replication running, it's an easy task to make a backup of the data. You just need to temporarily stop the slave server from replicating by entering the following SQL statement while logging onto the slave server as root or a user with SUPER privileges: 

> STOP SLAVE;

The slave server knows the position where it left off in the binary log of the master server. So we can take our time making a backup of the replicated databases on the slave server. We can use any backup utility or method we prefer. When the backup is finished, we would enter the following SQL statement from the slave server as root to restart replication:

> START SLAVE;

After entering this statement, there should be a flurry of activity on the slave as it executes the SQL statements that occurred while it was down. In a very short period of time it should be current

### Automating Backups

If replication and the backup process are working properly, we can write a simple shell script to stop replication, back up the data on the slave server, and start the slave again. Such a shell script would look something like this:

~~~~sh
#!/bin/sh

date = `date +%Y%m%d`

mysqladmin --user=root --password=my_pwd stop-slave

mysqldump --user=root --password=my_pwd --lock-all-tables \
      --all-databases > /backups/mysql/backup-${date}.sql

mysqladmin --user=root --password=my_pwd start-slave
~~~~

In this example, we're using the mysqladmin utility to stop and start replication on the slave. On the first line, you may have noticed that we're capturing the date using the system function date and putting it into a good format (e.g., 20050615). This variable is used with mysqldump in the script for altering the name of the dump file each day. Of course, you can set the file path and the name of the dump file to your preferences. Notice that the date function and the formatting codes are enclosed in back-ticks (`), not single quotes (').

This is a simple script. You may want to write something more elaborate and allow for error checking. You probably would also want to compress the dump files to save space and write them to a removable media like a tape or CD. Once you have your script set up, test it. If it works, you can add it to your crontab or whatever scheduling utility you use on your server.

### Conclusion

Replication is a useful administrative feature of MySQL. It's an excellent way to be assured of good regular backups of databases. There are more options and SQL statements available for replication than I was able to present here. I cover them individually in my book MySQL in a Nutshell. For active and large systems, you may want to set up more than one slave server for added protection of data. The configuration and concepts are the same for multiple slaves as it is for one slave. For extremely active and large databases, you might want to consider purchasing software like that offered by Emic. Their software costs a bit, but it does an excellent job of handing slave serves for backups and load balancing, especially.

### Notes

To make sure that the replication doesn't collides, make sure that the auto increment offset in my.cnf is set to even on one site, and on odd on the other

## Commands

+ Check number of connections : *show status like 'Con%'*
+ 

# HashSet

DATA => Hash() => bucket

| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 
|---|---|---|---|---|---|---|---|---|---
|d1 |d3 |   |   |d4 |   |   |   |   |   
|d2 |   |   |   |d5 |   |   |   |   |   
|   |   |   |   |d6 |   |   |   |   |   

Time : Average : O(1), worst O(n)

Space : O(n)

# Data Structures

## Stack

lifo
~~~
[][][][][][] <= Push
             => Pop 
~~~

In array stack : careful for loitering

## Queue

fifo
~~~
Queue => [][][][][][] => Dequeue
~~~

## Bloom filter

A Bloom filter is a data structure designed to tell you, rapidly and **memory-efficiently**, whether an element is present in a set. 

It looks like this :

| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |...| m 
|---|---|---|---|---|---|---|---|---|---
|   |   |   |   |   |   |   |   |   |   


When you enter something in the set, it goes trough multiples **hash functions** and each of them produce turn a cell of the array on. So if you add multiples elements they might eventually end up turning on cells that are already turned on.
You'll want your hash functions to be evenly and randomly distributed

### Example

~~~~
* Ant -> hash1() -> cell 1
* Ant -> hash2() -> cell 5
* Ant -> hash3() -> cell 9
~~~~
~~~~
* MassEffect -> hash1() -> cell 0
* MassEffect -> hash2() -> cell 4
* MassEffect -> hash3() -> cell 8
~~~~
~~~~
* Manjaro -> hash1() -> cell 1 (Already on)
* Manjaro -> hash2() -> cell 4 (Already on)
* Manjaro -> hash3() -> cell 9 (Already on)
~~~~

### Get

The element you're trying to get will go trough the same hash functions, this will tell you which cells to look for. If one of those is off, the element cannot be there. If all are on, there is a fairly good chance that it has already been added.

You may have **false positives** but **no false negative**. The error rate can be tuned by varying the lenght of the array and the number of hash functions

### Notes

* You can't remove an item from an hash function... 'cause the cells might have been turned on by other things too
* The more saturated the array become, the more false positive you're going to get.
* The bigger the array, the less probability of false positive
* The approximate false positive rate can be calculated with this :

### Tuning 

The probability of false positive : **(1 - e<sup>-kn/m</sup>)<sup><sup>k</sup></sup>** 

Ok this deserve some explanations :

**n** is the number of expected member
**k** is the number of hash functions
**m** is the number of bits

+ With an empty bit vector and a good hashing algorithm with an uniform distribution, the probability of a particular element ending up in a particular bit is just **1/m**

**1/m**

+ After inserting this element, the probability of a bit not being set is **1 - 1/m**

**1 - 1/m**

+ And this probability after performing **k** hash functions is **(1 - 1/m)<sup>***k***</sup>**

**(1 - 1/m)<sup>***k***</sup>**

+ If you insert **n** elements, the probability that this bit isn't set is **(1 - 1/m)<sup>k***.n***</sup>**

**(1 - 1/m)<sup>k***.n***</sup>**

+ On the opposite, the probability that is set is 

***1 -*** **(1 - 1/m)<sup>k***.n***</sup>**

+ It turns, and I need to dig on that, that **(1 - 1/m)<sup>k.n</sup>** is roughly equivalent to **1 - e<sup>-kn/m</sup>**

≈ **1 - e<sup>-kn/m</sup>**

+ When we try to figure out the probability of false positive, we want to know the probability that all **k** bits are set to one by other elements

+ So this probability, is the probability that a bit is set ^ the number of bits we will verify **(1 - e<sup>-kn/m</sup>)<sup><sup>***k***</sup></sup>** 

**(1 - e<sup>-kn/m</sup>)<sup><sup>***k***</sup></sup>** 

# XA

This is a **global transaction** that may span **multiple resources**. On the opposite, a non-XA transaction always involves only one resource. 

So it involves *a coordinating transaction manager*, with one or more databases (or other resources, like JMS) all involved in a single global transaction. It coordinates all of this through a protocol called Two Phase Commit (2PC). This protocol also has to be supported by the individual resources. 

# This is not the GRUB you are looking for

1. Download http://unetbootin.github.io/
2. Select Super Grub Disk
3. Boot on it, select the first option, "GNU/Linux".
4. On the a menu titled "GNU/Linux (Help)": Select "Fix Boot of GNU/Linux (GRUB)". 

# Install MySQL on CentOS

1. Install MySQL server and MySQL client : *yum install mysql-server mysql*
2. Start the MySQL service : */etc/init.d/mysql start*
3. Run MySQL secure installation : */usr/bin/mysql_secure_installation*
4. Make sure MySQL auto-starts upon a reboot : *chkconfig mysqld on*

# Useful commands

+ df -h as in **D**isk **F**ree -**h**uman readable
+ du -sh as in **D**isk **U**sage -**h**uman readable -**s**ummerize
+ sort -h as in sort -**h**uman-numeric-sort
+ /etc/init.d/iptables save
find /path/to/files* -mtime +5 -exec rm {} \;
+ /etc/init.d/iptables stop : deactivate firewall
+ chkconfig iptables off : deactivate firewall on boot
+ find . -type f -size +100M
+ Enable network on minimal CentOS : edit **etc/sysconfig/network-scripts-ifcfg-whatever** and set **ONBOOT=yes** then **/etc/init.d/network** restart
+ Download Oracle jdk with wget : wget --no-check-certificate --no-cookies - --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/7u51-b13/jdk-7u51-linux-x64.tar.gz
+ Enable EPEL Repository in CentOS : *wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm*, *rpm -ivh epel-release-6-8.noarch.rpm* then *yum repolist*
+ Show files that I wanted to delete but someone still has hold of them. **lsof | grep delete**
+ find /path/to/files* -mtime +5 -exec rm {} \;

# Java World

java:global/[<application-name>]/<module-name>/<bean-name>!<fully-qualified-bean-interface-name>

### Mockito

~~~~java
import static org.mockito.Mockito.*;

MyClass c = mock(MyClass.class);
when(o.method()).thenReturn("return");
~~~~

### Maven

+ run specific test : mvn **-Dtest=TestNameOrPattern** clean test