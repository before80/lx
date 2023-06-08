+++
title = "SQL"
weight = 14
date = 2023-06-08T14:02:14+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# SQL

https://linuxcommand.org/lc3_adv_sql.php

Okay kids, gird your grid for a big one.

The world as we know it is powered by data. Data, in turn, is stored in databases. Most everything we use computers for involves using data stored in some kind of database. When we talk about storing large amounts of data, we often mean *relational database management systems* (RDBMS). Banks, insurance companies, most every accounting system, and many, many websites use relational databases to store and organize their data.

The idea of relational data storage is generally credited to English computer scientist and IBM researcher E. F. Cobb, who proposed it in a paper in 1970. In the years that followed, a number of software companies built relational database systems with varying degrees of success. Around 1973, IBM developed a simplified and flexible method of managing relational databases called *Structured Query Language* (SQL, often pronounced “sequel”). Today the combination of RDBMS and SQL is a huge industry, generating many billions of dollars every year.

Relational databases are important to the history of Linux as well. It was the availability of open source database programs (such as MySQL) and web servers (most notably, Apache) that led to an explosion of Linux adoption in the early days of the world wide web.

In this adventure, we’re going to study the fundamentals of relational databases and use a popular command line program to create and manage a database of our own. The AWK adventure is a suggested prerequisite.

## A Little Theory

Before we can delve into SQL we have to look at what relational databases are and how they work.

### Tables

Simply put, a relational database is one or more *tables* containing columns and rows. Technically, the columns are known as *attributes* and the rows as *tuples*, but most often they are simply called columns and rows. In many ways, a table resembles the familiar spreadsheet. In fact, spreadsheet programs are often used to prepare and edit database tables. Each column contains data of a consistent type, for example, one column might consist of integers and another column strings. A table can contain any number of rows.

### Schemas

The design of a database is called its *schema* and it can be simple, containing just a single table or two, or it can be complex, containing many tables with complex interrelationships between them.

Let’s imagine a database for a bookstore consisting of three tables. The first is called `Customers`, the second is called `Items`, and the third is called `Sales`. The `Customers` table will have multiple rows with each row containing information about one customer. The columns include a customer number, first and last names, and the customer’s address. Here is such a table with just some made-up names:

```
Cust First   Last     Street                City        ST
---- ------- -------- --------------------- ----------- --
0001 Richard Stollman 1 Outonthe Street     Boston      MA
0002 Eric    Roymond  2 Amendment Road      Bunker Hill PA
0003 Bruce   Porens   420 Middleville Drive Anytown     US
```

The `Items` table lists our books and contains the item number, title, author, and price.

```
Item Title                                  Author        Price
---- -------------------------------------- ------------- -----
1001 Winning Friends and Influencing People Dale Carnegie 14.95
1002 The Communist Manifesto                Marx & Engels 00.00
1003 Atlas Shrugged                         Ayn Rand      99.99
```

As we go about selling items in our imaginary bookstore, we generate rows in the `Sales` table. Each sale generates a row containing the customer number, date and time of the sale, the item number, the quantity sold, and the total amount of the sale.

```
Cust Date_Time    Item Quan Total
---- ------------ ---- ---- ------
0002 202006150931 1003    1  99.99
0001 202006151108 1002    1   0.00
0003 202006151820 1001   10 149.50
```

### Keys

Now we might be wondering what the `Cust` and `Item` columns are for. They serve as *keys*. Keys are values that serve to uniquely identify a table row and to facilitate interrelationships between tables. Keys have some special properties. They must be both unique (that is, they can appear only once in a table and specifically identify a row) and they must also be immutable (they can never change). If they can’t meet these requirements, they cannot be keys. Some database implementations have methods of enforcing these requirements during table creation and keys can be formally specified. In the case of our bookstore database, the `Cust` column contains the keys for the `Customers` table and the `Item` column contains the keys for the `Items` table.

Knowing this about keys, we can now understand why the `Sales` table works the way it does. We can see for example that row 1 of the `Sales` table tells us that customer `0002` purchased 1 copy of item `1003` for $99.99. So why do we need special values for the keys? Why not, for instance, just use the customer’s name as the key? It’s because we can’t guarantee that the name won’t change, or that two people might have the same name. We can guarantee that an arbitrarily assigned value like our customer number is unique and immutable.

## Database Engines/Servers

There are a number of database servers available for Linux. The two most prominent are MySQL (and its fork MariaDB) and PostgreSQL. These database servers implement *client/server architecture*, a concept that became a hot topic in the 1990s. Database servers of this type run as server processes and clients connect to them over a network connection and send SQL statements for the server to carry out. The results of the database operations are returned to the client program for further processing and presentation. Many web sites use this architecture with a web server sending SQL statements to a separate database server to dynamically create web pages as needed. The famous *LAMP stack* consisting of Linux, Apache web server, MySQL, and PHP powered much of the early web.

For purposes of this adventure, setting up database servers such as MySQL and PostgreSQL is too complicated to cover here since, among other things, they support multiple concurrent users and their attendant security controls. It’s more than we need for just learning SQL.

## sqlite3

The database server we will be using is SQLite. SQLite is a library that can be used with applications to provide a high-level of SQL functionality. It’s very popular with the embedded systems crowd. It saves application developers the trouble of writing custom solutions to their data storage and management tasks. In addition to the library, SQLite provides a command line tool for directly interacting with SQLite databases. Also, since it accepts SQL from standard input (as well as it own command line interface) and sends its results to standard output, it’s ideal for use in our shell scripts.

SQLite is available from most Linux distribution repositories. Installation is easy, for example:

```
me@linuxbox:~$ sudo apt install sqlite3
```

### Building a Playground

Let’s build a playground and play with some real data. On the LinuxCommand.org site there is a archive we can download that will do the trick.

```
me@linuxbox:~$ cd
me@linuxbox:~$ curl -c http://linuxcommand.org/adventure-sql.tgz
me@linuxbox:~$ tar xzf adventure-sql.tgz
me@linuxbox:~$ cd adventure-sql
```

Extracting the `.tgz` file will produce the playground directory containing the data sets, some demonstration code, and some helpful tools. The data sets we will be working with contain the listings of installed packages on an Ubuntu 18.04 system. This will include the name of packages, a short description of each one, a list of files contained in each package, and their sizes.

```
me@linuxbox:~/adventure-sql$ ls
```

All the files are human-readable text, so feel free to give them a look. The data sets in the archive are the `.tsv` files. These are tab-separated value files. The first one is the `package_descriptions.tsv` file. This contains two columns of data; a package name and a package description. The second file, named `package_files.txv`, has three columns: a package name, the name of a file installed by the package and the size of the file.

### Starting sqlite3

To launch SQLite, we simply issue the command `sqlite3` followed optionally by the name of a file to hold our database of tables. If we do not supply a file name, SQLite will create a temporary database in memory.

```
me@linuxbox:~/advemture-sql$ sqlite3
SQLite version 3.22.0 2018-01-22 18:45:57
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite>
```

When loading is complete, SQLite will present a prompt where we can enter commands. Commands can be either SQL statements or *dot commands* that are used to control SQLite itself. To see a list of the available dot commands, we enter `.help` at the prompt.

```
sqlite> .help
```

There are only a few of the dot commands that will be of interest to us and they deal mainly with how output is formatted. To exit `sqlite3`, we enter the dot command `.quit` at the prompt.

```
sqlite> .quit
```

Though we can interact directly with the `sqlite3` program using the `sqlite>` prompt, `sqlite3` can also accept streams of dot commands and SQL statements through standard input. This is how SQLite is most often used.

### Creating a Table and Inserting Our Data

To get started with our database, we need to first convert our `.tsv` files into a stream of SQL statements. Our database will initially consist of two tables. The first is named `Package_Descriptions` and the second is named `Package_Files`. To create the SQL stream for the `Package_Descriptions` table we will use the `insert_Package_Descriptions.awk` program supplied in the playground archive.

```
me@linuxbox:~/advemture-sql$ ./insert_Package_Descriptions.awk \
< package_descriptions.tsv > insert_Package_Descriptions.sql
```

Let’s take a look at the resulting SQL stream. We’ll use the `head` command to display the first few lines of the stream.

```
me@linuxbox:~/advemture-sql$ head insert_Package_Descriptions.sql
DROP TABLE IF EXISTS Package_Descriptions;
CREATE TABLE Package_Descriptions (
    package_name VARCHAR(60),
    description  VARCHAR(120)
);
BEGIN TRANSACTION;
INSERT INTO Package_Descriptions
     VALUES ( 'a2ps',
              'GNU a2ps - ''Anything to PostScript'' converter and pretty-prin
ter');
INSERT INTO Package_Descriptions
     VALUES ( 'accountsservice',
              'query and manipulate user account information');
```

And the last few lines using the `tail` command.

```
me@linuxbox:~/advemture-sql$ tail insert_Package_Descriptions.sql
     VALUES ( 'zlib1g:amd64',
              'compression library - runtime');
INSERT INTO Package_Descriptions
     VALUES ( 'zlib1g:i386',
              'compression library - runtime');
INSERT INTO Package_Descriptions
     VALUES ( 'zlib1g-dev:amd64',
              'compression library - development');
INSERT INTO Package_Descriptions
     VALUES ( 'zsh',
              'shell with lots of features');
INSERT INTO Package_Descriptions
     VALUES ( 'zsh-common',
              'architecture independent files for Zsh');
COMMIT;
```

As we can see, SQL is verbose and somewhat English-like. Convention dictates that language keywords be in uppercase; however it is not required. SQL is case insensitive. White space is not important, but is often used to make the SQL statements easier to read. Statements can span multiple lines but don’t have to. Statements are terminated by a semicolon character. The SQL in this adventure is generally formatted in accordance with the style guide written by Simon Holywell linked in the “Further Reading” section below. Since some SQL can get quite complicated, visual neatness counts when writing code.

SQL supports two forms of commenting.

```
-- Single line comments are preceeded by 2 dashes

/* And multi-line comments are done in the
   style of the C programming language */
```

Before we go on, we need to digress for a moment to discuss SQL as a standard. While there are ANSI standards for SQL, every database server implements SQL differently. Each one has a slightly different dialect. The reason for this is partly historical; in the early days there weren’t any firm standards, and partly commercial. Each database vendor wanted to make it hard for customers to migrate to competing products so each vendor added unique extensions and features to the language to promote the dreaded “vendor lock-in” for their product. SQLite supports most of standard SQL (but not all of it) and it adds a few unique features.

### Creating and Deleting Tables

The first 2 lines of our SQL stream deletes any existing table named `Package_Descriptions` and creates a new table with that name. The `DROP TABLE` statement deletes a table. The optional `IF EXISTS` clause is used to prevent errors if the table does not already exist. There are a lot of optional clauses in SQL. The `CREATE TABLE` statement defines a new table. As we can see, this table will have 2 columns. The first column, `package_name` is defined to be a variable length string up to 60 characters long. `VARCHAR` is one of the available data types we can define. Here are some of the common data types supported by SQL databases:

| Data Type  | Description            |
| :--------- | :--------------------- |
| INTEGER    | Integer                |
| CHAR(n)    | Fixed length string    |
| VARCHAR(n) | Variable length string |
| NUMERIC    | Decimal numbers        |
| REAL       | Floating point numbers |
| DATETIME   | Date and time values   |

### Data Types

SQL databases support many types of data. Unfortunately, this varies by vendor. Even in cases where two databases share the same data type name, the actual meaning of the data type can differ. Data types in SQLite, unlike other SQL databases, are not rigidly fixed. Values in SQLite are *dynamically typed*. While SQLite allows many of the common data types found in other databases to be specified, it actually only supports 4 general types of data storage.

| Data Type | Description                                               |
| :-------- | :-------------------------------------------------------- |
| INTEGER   | Signed integers using 1, 2, 3, 4, 6, or 8 bytes as needed |
| REAL      | 8-byte IEEE floating point numbers                        |
| TEXT      | Text strings                                              |
| BLOB      | Binary large objects (for example JPEG, or MP3 files)     |

In our example above, we specified `VARCHAR` as the data type for our columns. SQLite is perfectly happy with this, but it actually stores the values in these columns as just `TEXT`. It ignores the length restrictions set in the data type specification. SQLite is extremely lenient about data types. In fact, it allows any kind of data to be stored in any specified data type, even allowing a mixture of data types in a single column. This is completely incompatible with all other databases, and relying on this would be very bad practice. In the remainder of this adventure we will be sticking to conventional data types and behavior.

### Inserting Data

Moving on with our SQL stream, we see that the majority of the stream consists of `INSERT` statements. This is how rows are added to a table. Insert is sort of a misnomer as `INSERT` statements append rows to a table.

We surround the `INSERT` statements with `BEGIN TRANSACTION` and `COMMIT`. This is done for performance reasons. If we leave these out, the rows will still be appended to the table but each `INSERT` will be treated as a separate transaction, vastly increasing the amount of time it takes to append a large number of rows. Treating a transaction this way also has another important benefit. SQL does not apply the transaction to the database until it receives the `COMMIT` statement, thus it is possible to write SQL code that will abandon a transaction if there is a problem and the change will be rolled back leaving the database unchanged.

Let’s go ahead and create our first table and add the package names and descriptions.

```
me@linuxbox:~/advemture-sql$ sqlite3 adv-sql.sqlite \
< insert_Package_Descriptions.sql
```

We execute the `sqlite3` program specifying `adv-sql.sqlite` as the file used to store our tables. The choice of file name is arbitrary. We read our SQL stream into standard input and `sqlite3` carries out the statements.

### Doing Some Queries

Now that we have a database (albeit a small one), let’s take a look at it. To do this, we will start up `sqlite3` and interact with it at the prompt.

```
me@linuxbox:~/advemture-sql$ sqlite3 adv-sql.sqlite
SQLite version 3.22.0 2018-01-22 18:45:57
Enter ".help" for usage hints.
sqlite> 
```

We’ll first use some SQLite dot commands to examine the structure of the database.

```
sqlite> .tables
Package_Descriptions
sqlite> .schema Package_Descriptions
CREATE TABLE Package_Descriptions (
    package_name VARCHAR(60),
    description  VARCHAR(120)
); 
```

The `.tables` dot command displays a list of tables in the database while the `.schema` dot command lists the statements used to create the specified table.

Next, we’ll get into some real SQL using `SELECT`, probably the most frequently used SQL statement.

```
sqlite> SELECT * FROM Package_Descriptions;
a2ps|GNU a2ps - 'Anything to PostScript' converter and pretty-printer
accountsservice|query and manipulate user account information
acl|Access control list utilities
acpi-support|scripts for handling many ACPI events
acpid|Advanced Configuration and Power Interface event daemon
adduser|add and remove users and groups
adium-theme-ubuntu|Adium message style for Ubuntu
adwaita-icon-theme|default icon theme of GNOME (small subset)
aisleriot|GNOME solitaire card game collection
alsa-base|ALSA driver configuration files
.
.
.
```

This is the simplest form of the `SELECT` statement. The syntax is the word `SELECT` followed by a list of columns or calculated values we want, and a `FROM` clause specifying the source of the data. This example uses `*` which means every column. Alternately, we could explicitly name the columns, like so:

```
sqlite> SELECT package_name, description FROM Package_Descriptions;
```

And achieve the same result.

### Controlling the Output

As we can see from the output above, the default format is fine for further processing by tools such as `awk`, but it leaves a lot to be desired when it comes to being read by humans. We can adjust the output format with some dot commands. We’ll also add the `LIMIT` clause to the end of our query to output just 10 rows.

```
sqlite> .headers on
sqlite> .mode column
sqlite> SELECT * FROM Package_Descriptions LIMIT 10;
package_name  description                                                     
------------  ----------------------------------------------------------------
a2ps          GNU a2ps - 'Anything to PostScript' converter and pretty-printer
accountsserv  query and manipulate user account information                   
acl           Access control list utilities                                   
acpi-support  scripts for handling many ACPI events                           
acpid         Advanced Configuration and Power Interface event daemon         
adduser       add and remove users and groups                                 
adium-theme-  Adium message style for Ubuntu                                  
adwaita-icon  default icon theme of GNOME (small subset)                      
aisleriot     GNOME solitaire card game collection                            
alsa-base     ALSA driver configuration files
```

By using the `.headers on` and `.mode column` dot commands, we add the column headings and change the output to column format. These settings stay in effect until we change them. The `.mode` dot command has a number of interesting possible settings.

| Mode   | Description                                                  |
| :----- | :----------------------------------------------------------- |
| csv    | Comma-separated values                                       |
| column | Left-aligned columns. Use .width n1 n2… to set column widths. |
| html   | HTML <table> code                                            |
| insert | SQL insert statements for TABLE                              |
| line   | One value per line                                           |
| list   | Values delimited by .separator string. This is the default.  |
| tabs   | Tab-separated values                                         |
| tcl    | TCL (Tool Control Language) list elements                    |

Here we will set the mode and column widths for our table.

```
sqlite> .mode column 
sqlite> .width 20 60 
sqlite> SELECT * FROM Package_Descriptions LIMIT 10;
package_name          description                                                 
--------------------  ---------------------------------------------------------
a2ps                  GNU a2ps - 'Anything to PostScript' converter and pretty-
accountsservice       query and manipulate user account information               
acl                   Access control list utilities                               
acpi-support          scripts for handling many ACPI events                       
acpid                 Advanced Configuration and Power Interface event daemon     
adduser               add and remove users and groups                             
adium-theme-ubuntu    Adium message style for Ubuntu                              
adwaita-icon-theme    default icon theme of GNOME (small subset)                  
aisleriot             GNOME solitaire card game collection                        
alsa-base             ALSA driver configuration files                             
```

In addition to listing columns, `SELECT` can be used to perform various output tasks. For example, we can perform calculations such as counting the number of rows in the `Package_Descriptions` table.

```
sqlite> SELECT COUNT(package_name) FROM Package_Descriptions;
count(package_name) 
--------------------
1972                
```

### Being Selective

We can make `SELECT` output rows based on some selection criteria. Either an exact match:

```
sqlite> SELECT * FROM Package_Descriptions WHERE package_name = 'bash';
package_name          description                                                 
--------------------  --------------------------------------------------------
bash                  GNU Bourne Again SHell                                  
```

A partial match using SQL wildcard characters:

```
sqlite> SELECT * FROM Package_Descriptions WHERE description LIKE '%bash%';
package_name          description
--------------------  --------------------------------------------------------
bash-completion       programmable completion for the bash shell
command-not-found     Suggest installation of packages in interactive bash ses
```

SQL supports two wildcard characters. The underscore (`_`), which matches any single character, and the percent sign (`%`), which matches zero or more instances of any character.

Notice too that strings are surrounded with single quotes. If a value is quoted this way, SQL treats it as a string. For example, the value `'123'` would be treated as a string rather than a number.

### Sorting Output

Unless we tell `SELECT` to sort our data, it will be listed in the order it was inserted into the table. Our data appears in alphabetical order by package name because it was inserted that way, not because of anything SQLite is doing. The `ORDER BY` clause can be added to determine which column is used for sorting. To demonstrate, let’s sort the output by the description,

```
sqlite> SELECT * FROM Package_Descriptions ORDER BY description LIMIT 10;
package_name          description
--------------------  -------------------------------------------------------
network-manager-conf
fonts-noto-cjk        "No Tofu" font families with large Unicode coverage (CJ
fonts-noto-mono       "No Tofu" monospaced font family with large Unicode cov
udev                  /dev/ and hotplug management daemon
procps                /proc file system utilities
alsa-base             ALSA driver configuration files
libasound2-plugins:a  ALSA library additional plugins
libhyphen0:amd64      ALTLinux hyphenation library - shared library
apcupsd               APC UPS Power Management (daemon)
apcupsd-doc           APC UPS Power Management (documentation/examples)
```

The default sorting order is ascending, but we can also sort in descending order by including `DESC` after the column name. Multiple columns can be named and `ASC` can be used to specify ascending order.

```
sqlite> SELECT * FROM Package_Descriptions ORDER BY description DESC LIMIT 10;
package_name  description                                       
------------  --------------------------------------------------
xsane-common  xsane architecture independent files              
libx264-152:  x264 video coding library                         
libevdev2:am  wrapper library for evdev devices                 
wireless-reg  wireless regulatory database                      
crda          wireless Central Regulatory Domain Agent          
libmutter-2-  window manager library from the Mutter window mana
libwayland-s  wayland compositor infrastructure - server library
libwayland-c  wayland compositor infrastructure - cursor library
libwayland-c  wayland compositor infrastructure - client library
libwayland-e  wayland compositor infrastructure - EGL library   
```

### Adding Another Table

To demonstrate more of what we can do with `SELECT`, we’re going to need a bigger database. We have a second `.tsv` file that we can add. To save a step, we’ll filter the file into SQL and pipe it directly into `sqlite3`.

```
me@linuxbox:~/advemture-sql$ ./insert_package_files.awk \ 
                                < package_files-deb.tsv \
                                | sqlite3 adv-sql.sqlite
```

The second table is named `Package_Files`. Here is its schema:

```
sqlite> .schema Package_Files
CREATE TABLE Package_Files (
    package_name VARCHAR(60),
    file         VARCHAR(120),
    size_bytes   INTEGER
);
```

As we can see, this table has 3 columns; the package name, the name of a file installed by the package, and the size of the installed file in bytes. Let’s do a `SELECT` to see how this table works.

```
sqlite> .headers on
sqlite> .mode column
sqlite> .width 15 50 -10
sqlite> SELECT * FROM Package_Files WHERE package_name = 'bash';
package_name     file                                                size_bytes
---------------  -------------------------------------------------  ----------
bash             /bin/bash                                             1113504
bash             /etc/bash.bashrc                                         2319
bash             /etc/skel/.bash_logout                                    220
bash             /etc/skel/.bashrc                                        3771
bash             /etc/skel/.profile                                        807
bash             /usr/bin/bashbug                                         7115
bash             /usr/bin/clear_console                                  10312
bash             /usr/share/doc/bash/COMPAT.gz                            7853
bash             /usr/share/doc/bash/INTRO.gz                             2921
bash             /usr/share/doc/bash/NEWS.gz                             27983
bash             /usr/share/doc/bash/POSIX.gz                             3702
bash             /usr/share/doc/bash/RBASH                                1693
bash             /usr/share/doc/bash/README                               3839
bash             /usr/share/doc/bash/README.Debian.gz                     1919
bash             /usr/share/doc/bash/README.abs-guide                     1105
bash             /usr/share/doc/bash/README.commands.gz                   3021
bash             /usr/share/doc/bash/changelog.Debian.gz                  1357
bash             /usr/share/doc/bash/copyright                           10231
bash             /usr/share/doc/bash/inputrc.arrows                        727
bash             /usr/share/lintian/overrides/bash                         156
bash             /usr/share/man/man1/bash.1.gz                           86656
bash             /usr/share/man/man1/bashbug.1.gz                          804
bash             /usr/share/man/man1/clear_console.1.gz                   1194
bash             /usr/share/man/man1/rbash.1.gz                            154
bash             /usr/share/man/man7/bash-builtins.7.gz                    508
bash             /usr/share/menu/bash                                      194
bash             /bin/rbash                                                  4
```

Notice the `.width` dot command above. A negative value causes the corresponding column to be right-aligned.

### Subqueries

The `SELECT` statement can be used to produce many kinds of output. For example, it can be used to simply print literal strings.

```
sqlite> .mode column
sqlite> .header off
sqlite> SELECT 'String 1', 'String 2';
string 1    string 2
```

As we saw before, `SELECT` can produce calculated values.

```
sqlite> .header on
sqlite> SELECT 2 + 2;
2 + 2
--------------------
4
sqlite> SELECT COUNT(file), AVG(size_bytes) FROM Package_Files;
count(file)  avg(size_bytes)
-----------  ----------------
153506       33370.3488658424
```

To make complicated expressions more readable, we can assign their results to *aliases* by using the `AS` clause.

```
sqlite> SELECT COUNT(file) AS Files,
   ...> AVG(size_bytes) AS 'Average Size'
   ...> FROM Package_Files;
Files       Average Size    
----------  ----------------
153506      33370.3488658424
```

An important feature of `SELECT` is the ability to produce results by combining data from multiple tables. This process is done by performing *joins* and *subqueries*. We’ll talk about joins a little later, but for now let’s concentrate on subqueries. `SELECT` allows us to include another `SELECT` statement as an item to output. To demonstrate this, we will produce a table that includes columns for package name, number of files in the package, and the total size of the package. The `SELECT` statement to do this is rather formidable. We’ll open our text editor and create a file named `subquery_demo1.sql` with the following content:

```
-- subquery_demo1.sql

-- Query to list top 20 packages with the greatest numbers of files

.mode column
.header on
.width 20 40 10 10
SELECT package_name, description,
       (SELECT COUNT(file)
          FROM Package_Files
         WHERE Package_Descriptions.package_name = Package_Files.package_name)
            AS files,
       (SELECT SUM(size_bytes)
          FROM Package_Files
         WHERE Package_Descriptions.package_name = Package_Files.package_name)
            AS size
  FROM Package_Descriptions ORDER BY files DESC LIMIT 20;
```

We’ll next run this query and view the results.

```
me@linuxbox:~/adventure-sql$ sqlite3 adv-sql.sqlite < subquery_demo1.sql
package_name          description                       files       size      
--------------------  --------------------------------  ----------  ----------
linux-headers-4.15.0  Header files related to Linux ke  14849       63991787  
linux-headers-4.15.0  Header files related to Linux ke  14849       64001943  
humanity-icon-theme   Humanity Icon theme               8014        14213715  
linux-headers-4.15.0  Linux kernel headers for version  7861        9015084   
linux-headers-4.15.0  Linux kernel headers for version  7860        9025673   
linux-modules-extra-  Linux kernel extra modules for v  4173        165921470 
linux-modules-extra-  Linux kernel extra modules for v  4172        165884678 
libreoffice-common    office productivity suite -- arc  3551        76686149  
gnome-accessibility-  High Contrast GTK+ 2 theme and i  3464        3713621   
ubuntu-mono           Ubuntu Mono Icon theme            3025        3755093   
ncurses-term          additional terminal type definit  2727        1987483   
manpages-dev          Manual pages about using GNU/Lin  2101        2192620   
linux-firmware        Firmware for Linux kernel driver  1938        331497257 
tzdata                time zone and daylight-saving ti  1834        1210058   
vim-runtime           Vi IMproved - Runtime files       1642        27941732  
codium                Code editing. Redefined.          1307        271907088 
zsh-common            architecture independent files f  1256        12261077  
perl-modules-5.26     Core Perl modules                 1144        18015966  
adwaita-icon-theme    default icon theme of GNOME (sma  1127        4848678   
gimp-data             Data files for GIMP               1032        45011675
```

The query takes some time to run (it has a lot to do) and from the results we see that it produces 4 columns: package name, description, number of files in the package, and total size of the package. Let’s take this query apart and see how it works. At the uppermost level we see that the query follows the normal pattern of a `SELECT` statement.

```
SELECT list_of_items FROM Package_Descriptions
ORDER BY total_files DESC
LIMIT 20;
```

The basic structure is simple. What’s interesting is the `list_of_items` part. We know the list of items is a comma-separated list of items to output, so if we follow the commas we can see the list:

1. `package_name`
2. `description`
3. `(SELECT COUNT(file) FROM Package_Files WHERE Package_Descriptions.package_name = Package_Files.package_name) AS files`
4. `(SELECT SUM(size_bytes) FROM Package_Files WHERE Package_Descriptions.package_name = Package_Files.package_name) AS size`

It’s also possible to use a subquery in a `WHERE` clause. Consider this query that we will name `subquery_demo2.sql`:

```
-- subquery_demo2.sql

-- Query to list all packages containing more than 1000 files

.mode column
.header on
.width 20 60
SELECT package_name, description
  FROM Package_Descriptions
 WHERE 1000 < (SELECT COUNT(file)
          FROM Package_Files
         WHERE Package_Descriptions.package_name = Package_Files.package_name)
 ORDER BY package_name;
```

When we execute this, we get the following results:

```
me@linuxbox:~/adventure-sql$ sqlite3 adv-sql.sqlite < subquery_demo2.sql
package_name          description                                                 
--------------------  --------------------------------------------------------
adwaita-icon-theme    default icon theme of GNOME (small subset)              
codium                Code editing. Redefined.                                
gimp-data             Data files for GIMP                                     
gnome-accessibility-  High Contrast GTK+ 2 theme and icons                    
humanity-icon-theme   Humanity Icon theme                                     
inkscape              vector-based drawing program                            
libreoffice-common    office productivity suite -- arch-independent files     
linux-firmware        Firmware for Linux kernel drivers                       
linux-headers-4.15.0  Header files related to Linux kernel version 4.15.0     
linux-headers-4.15.0  Linux kernel headers for version 4.15.0 on 64 bit x86 SM
linux-headers-4.15.0  Header files related to Linux kernel version 4.15.0     
linux-headers-4.15.0  Linux kernel headers for version 4.15.0 on 64 bit x86 SM
linux-modules-4.15.0  Linux kernel extra modules for version 4.15.0 on 64 bit 
linux-modules-4.15.0  Linux kernel extra modules for version 4.15.0 on 64 bit 
linux-modules-extra-  Linux kernel extra modules for version 4.15.0 on 64 bit 
linux-modules-extra-  Linux kernel extra modules for version 4.15.0 on 64 bit 
manpages-dev          Manual pages about using GNU/Linux for development      
ncurses-term          additional terminal type definitions                     
perl-modules-5.26     Core Perl modules                                        
tzdata                time zone and daylight-saving time data                  
ubuntu-mono           Ubuntu Mono Icon theme                                   
vim-runtime           Vi IMproved - Runtime files                              
zsh-common            architecture independent files for Zsh
```

### Updating Tables

The `UPDATE` statement is used to change values in one or more existing rows. We will demonstrate this by adding 100 to the size of each file in the `sqlite3` package. First, let’s look at the files in the package.

```
sqlite> .mode column
sqlite> .header on
sqlite> .width 50 -10
sqlite> SELECT file, size_bytes FROM Package_Files
   ...> WHERE package_name = 'sqlite3';
file                                                size_bytes
--------------------------------------------------  ----------
/usr/bin/sqldiff                                       1103280
/usr/bin/sqlite3                                       1260976
/usr/share/doc/sqlite3/copyright                          1261
/usr/share/man/man1/sqlite3.1.gz                          3596
/usr/share/doc/sqlite3/changelog.Debian.gz                  35
```

Next, we’ll update the table, adding 100 to the size of each file.

```
sqlite> UPDATE Package_Files SET size_bytes = size_bytes + 100
   ...> WHERE package_name = 'sqlite3';
```

When we examine the rows now, we see the change.

```
sqlite> SELECT file, size_bytes FROM Package_Files
   ...> WHERE package_name = 'sqlite3';
file                                                size_bytes
--------------------------------------------------  ----------
/usr/bin/sqldiff                                       1103380
/usr/bin/sqlite3                                       1261076
/usr/share/doc/sqlite3/copyright                          1361
/usr/share/man/man1/sqlite3.1.gz                          3696
/usr/share/doc/sqlite3/changelog.Debian.gz                 135
```

Finally, we’ll subtract 100 from each row to return the sizes to their original values.

```
sqlite> UPDATE Package_Files SET size_bytes = size_bytes - 100
   ...> WHERE package_name = 'sqlite3';
```

`UPDATE` can modify multiple values at once. To demonstrate this, we will create a new table called `Package_Stats` and use `UPDATE` to fill in the values. Since this one is a little complicated, we will put this in a file named `create_Package_Stats.sql`.

```
-- create_Package_Stats.sql

DROP TABLE IF EXISTS Package_Stats;
CREATE TABLE Package_Stats (
    package_name  VARCHAR(60),
    count         INTEGER,
    tot_size      INTEGER,
    min_size      INTEGER,
    max_size      INTEGER,
    avg_size      REAL
);

INSERT INTO Package_Stats (package_name)
     SELECT package_name
       FROM Package_Descriptions;

UPDATE Package_Stats
   SET count = (SELECT COUNT(file)
                  FROM Package_Files
                 WHERE Package_Files.package_name =
                       Package_Stats.package_name),
    tot_size = (SELECT SUM(size_bytes)
                  FROM Package_Files
                 WHERE Package_Files.package_name = 
                       Package_Stats.package_name),
    min_size = (SELECT MIN(size_bytes)
                  FROM Package_Files
                 WHERE Package_Files.package_name = 
                       Package_Stats.package_name),
    max_size = (SELECT MAX(size_bytes)
                  FROM Package_Files
                 WHERE Package_Files.package_name = 
                       Package_Stats.package_name),
    avg_size = (SELECT AVG(size_bytes)
                  FROM Package_Files
                 WHERE Package_Files.package_name = 
                       Package_Stats.package_name);
```

This file consists of four SQL statements. The first two are used to create the new table, as we have seen before. The third statement is an alternate form of the `INSERT` statement. This form is useful, as it copies a value from one table into another. This `INSERT` will create all the rows we need but only fill in the `package_name` column. To fill in the rest, we will use an `UPDATE` that fills in the remaining five values based on the results of some queries of the `Package_Files` table. Note that without a `WHERE` clause, `UPDATE` applies changes to every row.

Once the table is constructed, we can examine its contents.

```
sqlite> .width 25 -5 -10 -8 -8 -10
sqlite> SELECT * FROM Package_Stats LIMIT 10;
package_name               count    tot_size  min_size  max_size    avg_size
-------------------------  -----  ----------  --------  --------  ----------
a2ps                         299     3455890       117    388096  11558.1605
accountsservice               19      261704        42    182552  13773.8947
acl                           11       91106        30     35512  8282.36363
acpi-support                  18       13896        67      4922       772.0
acpid                         19       86126       115     52064  4532.94736
adduser                       81      246658         7     37322  3045.16049
adium-theme-ubuntu           137      126759        25     12502  925.248175
adwaita-icon-theme          1127     4848678        30     87850  4302.28748
aisleriot                    316     1890864        47    281544  5983.74683
alsa-base                     42      195295        24     34160  4649.88095
```

We’ll come back to this table a little later when we take a look at joins.

### Deleting Rows

Deleting rows is pretty easy in SQL. There is a `DELETE` statement with a `WHERE` clause to specify a target. We’ll demonstrate that, but first there’s a nifty trick that SQLite supports.

We can change the output mode to write out `INSERT` statements. Let’s try it out.

```
sqlite> .mode insert Package_Files
```

If we use this `.mode` setting, we tell SQLite that we want `INSERT` statements directed at the specified table, in this case, `Package_Files`. Once we set this output mode, we can see the result.

```
sqlite> SELECT * FROM Package_Files WHERE package_name = 'sqlite3';
INSERT INTO Package_Files VALUES('sqlite3','/usr/bin/sqldiff',1103380);
INSERT INTO Package_Files VALUES('sqlite3','/usr/bin/sqlite3',1261076);
INSERT INTO Package_Files VALUES('sqlite3','/usr/share/doc/sqlite3/copyright',1
361);
INSERT INTO Package_Files VALUES('sqlite3','/usr/share/man/man1/sqlite3.1.gz',3
696);
INSERT INTO Package_Files VALUES('sqlite3','/usr/share/doc/sqlite3/changelog.De
bian.gz',135);
```

We’ll repeat this `SELECT`, but first we’ll change the output from standard output to a file named `insert_sqlite3.sql`.

```
sqlite> .output insert_sqlite3.sql
sqlite> select * from Package_Files where package_name = 'sqlite3';
```

This will write the stream of `INSERT` statements to the specified file. Next we’ll set the output back to standard output by issuing the `.output` dot command without an output file name.

```
sqlite> .output
```

Now let’s delete the rows in the `Package_Files` table.

```
sqlite> DELETE FROM Package_Files WHERE package_name = 'sqlite3';
```

We can confirm the deletion by running our query again and we see an empty result.

```
sqlite> .header on
sqlite> .mode column
sqlite> .width 12 50 -10
sqlite> SELECT * FROM Package_Files WHERE package_name = 'sqlite3';
sqlite>
```

Since we saved an SQL stream that can restore the deleted rows, we can now put them back in the table. The `.read` dot command can read the stream and execute it as though it came from standard input.

```
sqlite> .read insert_sqlite3.sql
```

Now when we run our query, we see that the rows have been restored.

```
sqlite> SELECT * FROM Package_Files WHERE package_name = 'sqlite3';
package_name  file                                                size_bytes
------------  --------------------------------------------------  ----------
sqlite3       /usr/bin/sqldiff                                       1103280
sqlite3       /usr/bin/sqlite3                                       1260976
sqlite3       /usr/share/doc/sqlite3/copyright                          1261
sqlite3       /usr/share/man/man1/sqlite3.1.gz                          3596
sqlite3       /usr/share/doc/sqlite3/changelog.Debian.gz                  35
```

### Adding and Deleting Columns

SQL provides the `ALTER TABLE` statement to modify table’s schema. To demonstrate this, we will add a couple of columns to the `Package_Descriptions` table and fill them with values calculated from the `Package_Files` table. We’ll place the necessary SQL in the `add_column.sql` file.

```
-- add_column.sql

-- Add and populate columns to Package_Descriptions

ALTER TABLE Package_Descriptions ADD COLUMN files INTEGER;
ALTER TABLE Package_Descriptions ADD COLUMN size INTEGER;

UPDATE Package_Descriptions
   SET files = (SELECT COUNT(file)
                  FROM Package_Files
                 WHERE Package_Files.package_name =
                       Package_Descriptions.package_name),
        size = (SELECT SUM(size_bytes)
                  FROM Package_Files
                 WHERE Package_Files.package_name =
                       Package_Descriptions.package_name);
```

We’ll execute the statements and examine resulting schema.

```
sqlite> .read add_column.sql
sqlite> .schema
CREATE TABLE Package_Descriptions (
    package_name VARCHAR(60),
    description  VARCHAR(120),
    files        INTEGER,
    size         INTEGER);

sqlite> SELECT * FROM Package_Descriptions WHERE package_name = 'sqlite3';
package_name  description                          files       size      
------------  -----------------------------------  ----------  ----------
sqlite3       Command line interface for SQLite 3  5           2369648 
```

SQL provides another `ALTER TABLE` statement for deleting columns from a table. It has the following form:

```
ALTER TABLE table_name DROP COLUMN column_name;
```

Unfortunately, SQLite does not support this so we have to do it the hard way. This is accomplished in four steps:

1. Create a new temporary table with the desired schema.
2. Copy the columns we want to keep into the temporary table.
3. Drop the original table.
4. Rename the temporary table.

Here is a file called `drop_column.sql` that does the job.

```
-- drop_column.sql

-- Remove extra columns from Package_Descriptions

BEGIN TRANSACTION;

-- Create new table with temporary name

DROP TABLE IF EXISTS temp;
CREATE TABLE temp (
    package_name VARCHAR(60),
    description  VARCHAR(120));

-- Copy columns we want into new table

INSERT INTO temp
    (package_name, description)
    SELECT package_name, description
      FROM Package_Descriptions;

-- Get rid of old table and rename the new replacement table

DROP TABLE Package_Descriptions;

ALTER TABLE temp RENAME TO Package_Descriptions;

COMMIT;
```

We again use the alternate form of the `INSERT` statement for copying values from one table to another. We copy the `package_name` and `description` columns from the `Package_Descriptions` table to the correspondingly named columns in the `temp` table.

### Joins

A join is a method by which we perform a query and produce a result that combines the data from two tables. SQLite supports several types of joins but we’re going to focus on the most commonly used type called an *inner join*. We can think of an inner join as the intersection of two tables. In the example below, a file called `join_demo.sql`, we will produce a query result that matches our earlier example when we added columns to the `Package_Descriptions` table.

```
-- join_demo.sql

-- Demonstrate join by selecting columns from 2 tables

.header on
.mode column
.width 20 35 -5 -10

SELECT Package_Descriptions.package_name AS Package,
       description AS Description,
       count AS Files,
       tot_size AS Size
  FROM Package_Descriptions
  INNER JOIN Package_Stats
          ON Package_Descriptions.package_name =
             Package_Stats.package_name
LIMIT 10;
```

The results of this query are as follows:

```
Package               Description                          Files        Size
--------------------  -----------------------------------  -----  ----------
a2ps                  GNU a2ps - 'Anything to PostScript'    299     3455890
accountsservice       query and manipulate user account i     19      261704
acl                   Access control list utilities           11       91106
acpi-support          scripts for handling many ACPI even     18       13896
acpid                 Advanced Configuration and Power In     19       86126
adduser               add and remove users and groups         81      246658
adium-theme-ubuntu    Adium message style for Ubuntu         137      126759
adwaita-icon-theme    default icon theme of GNOME (small    1127     4848678
aisleriot             GNOME solitaire card game collectio    316     1890864
alsa-base             ALSA driver configuration files         42      195295
```

If we break down this query, we see that it starts out as we expect, then it is followed by the `INNER JOIN` statement. The `INNER` keyword is optional as an inner join is the default. After the `INNER JOIN` we have to specify the relationship on which the join is based. In this example, we are looking for matching instances of the `package_name` in both tables. Any expression my be used to convey the table relationship, provided that the result of the expression is true or false.

Since `package_name` is a column in both the `Package_Descriptions` and `Package_Stats` tables, we must precede it with the name of the respective table to avoid ambiguity.

### Views

The join example above is a pretty useful query for our tables, but due to its complexity it’s best executed from a file rather than as an ad hoc query. SQL addresses this issue by providing a feature called *views* that allows a complex query to be stored in the database and used to produce a virtual table that can be used with simple query commands. In the following example we will create a view using our `INNER JOIN` query above to create a virtual table called `Stats` that we can use as the target of subsequent queries.

```
-- view_demo.sql

DROP VIEW IF EXISTS Stats;
CREATE VIEW Stats
AS
    SELECT Package_Descriptions.package_name AS Package,
           description AS Description,
           count AS Files,
           tot_size AS Size
      FROM Package_Descriptions
      INNER JOIN Package_Stats
              ON Package_Descriptions.package_name =
                 Package_Stats.package_name
        ORDER BY Package;
```

Once our view is created, we can treat `Stats` as just another table in our database even though it does not really exist as such.

```
sqlite> .header on
sqlite> .mode column
sqlite> .width 20 35 -5 -10
sqlite> SELECT * FROM Stats LIMIT 10;
Package               Description                          Files        Size      
--------------------  -----------------------------------  -----  ----------
a2ps                  GNU a2ps - 'Anything to PostScript'    299     3455890   
accountsservice       query and manipulate user account i     19      261704    
acl                   Access control list utilities           11       91106     
acpi-support          scripts for handling many ACPI even     18       13896     
acpid                 Advanced Configuration and Power In     19       86126     
adduser               add and remove users and groups         81      246658    
adium-theme-ubuntu    Adium message style for Ubuntu         137      126759    
adwaita-icon-theme    default icon theme of GNOME (small    1127     4848678   
aisleriot             GNOME solitaire card game collectio    316     1890864   
alsa-base             ALSA driver configuration files         42      195295
```

To delete a view we use the `DROP VIEW` statement as follows:

```
sqlite> DROP VIEW Stats;
```

### Indexes

It’s been said that the three most important features of a database system are “performance, performance, and performance.” While this in not exactly true (things like data integrity and reliability are important, too), complex operations on large databases can get really slow, so it’s important to make things as fast as we can. One feature we can take advantage of are *indexes*.

An index is a data structure the database maintains that speeds up database searches. It’s a sorted list of rows in a table ordered by elements in one or more columns. Without an index, a table is sorted according to values in a hidden column called `rowid`. The values in this column are integers that start with 1 and increment each time a row is added to the table. Here we see a query that selects the 100th row from the `Package_Stats` table.

```
sqlite> .header on
sqlite> .mode column
sqlite> .width 20 -5 -8 -8 -8 -8
sqlite> SELECT * FROM Package_Stats WHERE rowid = 100;
package_name          count  tot_size  min_size  max_size  avg_size
--------------------  -----  --------  --------  --------  --------
cups-server-common      595   1996400         0    370070  3355.294
```

Our database server can locate this row in the table very quickly because it already knows where to find the 100th row. However, if we want to search for the row that contains package name `cups-server-common`, SQLite must examine every row in the table to locate the matching row. To facilitate performance tuning, SQLite provides a way to see what search strategy is used during a query.

```
sqlite> .width -8 -5 -4 55 
sqlite> EXPLAIN QUERY PLAN
   ...> SELECT * FROM Package_Stats WHERE package_name = 'cups-server-common';
selectid  order  from  detail
--------  -----  ----  ------------------------------------------------------- 
       0      0     0  SCAN TABLE Package_Stats
```

We can see from the `SCAN TABLE Package_Stats` the SQLite performs a sequential search of the table during this query.

To create an index to allow faster searches of `package_name` we can do the following:

```
sqlite> CREATE INDEX idx_package_name
   ...> ON Package_Stats (package_name);
```

After doing this, we’ll look at the query plan and see the difference.

```
sqlite> EXPLAIN QUERY PLAN
   ...> SELECT * FROM Package_Stats WHERE package_name = 'cups-server-common';
   selectid  order  from  detail                                                 
   --------  -----  ----  -------------------------------------------------------
          0      0     0  SEARCH TABLE Package_Stats USING INDEX idx_package_name
```

Hereafter, when we search the table for a package name, SQLite will use the index to directly get to the row rather than looking at every row searching for a match. So why don’t we just index everything? The reason we don’t is that indexes impose overhead every time a row is inserted, deleted, or updated since the indexes must be kept up to date. Indexes are best used on tables that are read more often than written to.

We probably won’t see much of a performance improvement when searching the `Package_Stats` table because it’s just not that big, but on a large table the improvement can be substantial.

We can see the index when we examine the table’s schema.

```
sqlite> .schema Package_Stats
CREATE TABLE Package_Stats (
    package_name  VARCHAR(60),
    count         INTEGER,
    tot_size      INTEGER,
    min_size      INTEGER,
    max_size      INTEGER,
    avg_size      REAL
);
CREATE INDEX idx_package_name
ON Package_Stats (package_name);
```

SQLite also has a dot command.

```
sqlite> .indexes
idx_package_name
```

Another benefit of using an index is that it’s kept in sorted order (that’s how it performs searches quickly). The side effect is that when an index is used during a query the results of the query will be sorted as well. To demonstrate, we’ll create another index for the `Package_Stats` table, this time using the `tot_size` column. Notice that when we perform a select based on that column, the results are in ascending order.

```
sqlite> CREATE INDEX idx_tot_size
   ...> ON Package_Stats (tot_size);
sqlite> .width 20 -5 -10 -8 -8 -8
sqlite> SELECT * FROM Package_Stats
   ...> WHERE tot_size > 100000000;
   package_name          count    tot_size  min_size  max_size avg_size
   --------------------  -----  ----------  --------  -------- --------
   inkscape               1025   127507308         0  19599216 124397.3
   libreoffice-core        119   135106135        26  66158968 1135345.
   linux-modules-extra-   4172   165884678      1292   4216105 39761.42
   linux-modules-extra-   4173   165921470      1292   4216105 39760.71
   thunderbird              69   180861838        27  12163098 2621186.
   firefox                  74   203393773        23  12408360 2748564.
   google-chrome-stable    100   235727530        25  16288078 2357275.
   libgl1-mesa-dri:amd6     20   237774005        36  19548840 11888700
   codium                 1307   271907088        17  11551467 208039.0
   linux-firmware         1938   331497257         6  19922416 171051.2
```

To delete our indexes, we use the `DROP INDEX` statement.

```
sqlite> DROP INDEX idx_package_name;
sqlite> DROP INDEX idx_tot_size;
```

### Triggers and Stored Procedures

As we saw earlier during our discussion of views, SQL allow us to store SQL code in the database. Besides views, SQL provides for two other ways of storing code. These two methods are *stored procedures* and *triggers*. Stored procedures, as the name implies, allows a block of SQL statements to be stored and treated as a subroutine available to other SQL programs, or for use during ad hoc interactions with the database. Creating a stored procedure is done with this syntax:

```
CREATE PROCEDURE procudure_name
AS
    [block of SQL code];
```

Parameters can be passed to stored procedures. Here is an example:

```
CREATE PROCEDURE list_pkg_files @package VARCHAR(60)
AS
    SELECT package_name, file
      FROM Package_Files
     WHERE package_name = @package; 
```

To call this procedure, we would do this:

```
EXEC list_package_files @package = 'bash';
```

Unfortunately, SQLite does not support stored procedures. It does, however, support the second method of code storage, triggers.

Triggers are stored blocks of code that are automatically called when some event occurs and a specified condition is met. Triggers are typically used to perform certain maintenance tasks to keep the database in good working order.

Triggers can be set to activate before, after, or instead of the execution of `INSERT`, `DELETE`, or `UPDATE` statements. In the example below, we will have a trigger activate before a `DELETE` is performed on the `Package_Files` table.

```
/*
    trigger_demo.sql

    Trigger demo where we create a "trash can" for the
    Package_Files table and set a trigger to copy rows
    to the PF_Backup table just before they are deleted
    from Package_Files.
*/

-- Create backup table with the same schema as Package_Files

CREATE TABLE IF NOT EXISTS PF_Backup (
    package_name VARCHAR(60),
    file         VARCHAR(120),
    size_bytes   INTEGER
);

-- Define trigger to copy rows into PF_Backup as they are
-- deleted from Package_Files

CREATE TRIGGER backup_row_before_delete
BEFORE DELETE ON Package_Files
BEGIN
    INSERT INTO PF_Backup
    VALUES (OLD.package_name, OLD.file, OLD.size_bytes);
END;
```

The first thing we do is create a table to hold our deleted rows. We use a slightly different form of the `CREATE TABLE` statement to create the table only if it does not already exist. This will ensure that an existing table of saved rows will persist, even if we reload the trigger.

After creating the table, we create a trigger called `backup_row_before_delete` to copy data from the `Package_Files` table to the `PF_Backup` table just before any row in `Package_Files` is deleted.

In order to reference data that might be used by the trigger, SQL provides the `NEW` reference for new data that is inserted or updated, and the `OLD` reference for previous data that is updated or deleted. In our example, we use the `OLD` reference to refer to the data about to be deleted.

### Performing Backups

Since SQLite uses an ordinary file to store each database (as opposed to the exotic methods used by some other systems), we can use regular command line tools such as `cp` to perform database backups. There is an interesting SQL method we can use, too. The `.dump` dot command will produce a stream of SQL statements that will fully reproduce the database including tables, views, triggers, etc. To output the database this way, we need only do the following:

```
sqlite> .dump
```

The stream will appear on standard output or we can use the `.output` dot command to direct the stream to the file of our choice.

One interesting application of this technique would be to combine tables from multiple databases into one. For example, let’s imagine we had several Raspberry Pi computers each performing data logging of an external sensor. We could collect dumps from each machine and combine all of the tables into a single database for data analysis and reporting.

### Generating Your Own Datasets

Below are the programs used to create the datasets used in this adventure. They are included in the archive for those who want to create their own datasets.

#### For Deb-based Systems (Debian, Ubuntu, Mint, Raspberry Pi OS)

The first program named `mk_package_descriptions-deb`, extracts package information and outputs a `.tsv` file.

```
#!/bin/bash

# mk_package_descriptions-deb - Output tsv list of installed debian/ubnutu
#                               packages on standard output

phase1() { # Replace repeated spaces with a tab
  awk '
  {
    gsub(/[ ][ ]+/, "\t")
    print $0
  }'
  return
}

phase2() { # Output field 2 and 5 separated by a tab
  awk '
    BEGIN {
      FS = "\t"
    }

    $1 == "ii" {
      print $2 "\t" $5
    }'
  return
}

dpkg-query -l | phase1 | phase2
```

The second program, `mk_package_files-deb` outputs all the files included in each package.

```
#!/bin/bash

# mk_package_files - make list of files in all packages
# Reads *.list files in LIST_DIR. Outputs stream of tsv to stdout.

LIST_DIR=/var/lib/dpkg/info

mk_list () {

  local list_file="$1"
  local lf_length="${#list_file}"
  local len
  local package
  local -a files

  ((len = lf_length - 5))
  package="${list_file:0:$len}" # strip '.list' extension
  package="${package##*/}" # strip leading pathname
  mapfile files < "$list_file" # load list into array
  for i in "${files[@]}"; do
    i="${i//[$'\t\r\n']}" # strip trailing newlines
    if [[ -f "$i" ]] ; then # write tsv file
      printf "%s\t%s\t%s\n" \
        "$package" \
        "$i" \
        "$(stat --printf '%s' "$i")" # size of file
    fi
  done
  return
}

for i in "$LIST_DIR"/*.list; do
  mk_list "$i"
done
```

#### For RPM-based Systems (RHEL, CentOS, Fedora)

The `mk_package_descriptions-rpm` script:

```
#!/bin/bash

# mk_package_descriptions-rpm - Output tsv list of installed Fedora/CentOS
#                               packages on standard output

while read package; do
  description=$(dnf info "$package" \
    | awk '$1 == "Summary" { gsub(/Summary      : /, ""); print $0; exit }')
  printf "%s\t%s\n" \
    "$package" \
    "$description"
done < <( dnf list installed | awk 'NF == 3 { print $1 }' )
```

The `mk_package_files-rpm` script:

```
#!/bin/bash

# mk_package_files-rpm - Output tsv list of installed Fedora/CentOS files
#                        on standard output

while read package; do
  while read package_file; do
    if [[ -r "$package_file" ]]; then # not all files are present/readable
      printf "%s\t%s\t%s\n" \
        "$package" \
        "$package_file" \
        "$(stat --printf '%s' "$package_file")"
    fi
  done < <( rpm -ql "$package" )
done < <( dnf list installed | awk 'NF == 3 { print $1 }' )
```

#### Converting .tsv to SQL

Below are two AWK programs used to convert the `.tsv` files into SQL. First, the `insert_package_descriptions.awk` program:

```
#!/usr/bin/awk -f

# insert_package_descriptions.awk - Insert records from
#                                   package_descriptions.tsv

BEGIN {
    FS="\t"
    print "DROP TABLE IF EXISTS Package_Descriptions;"
    print "CREATE TABLE Package_Descriptions ("
    print "    package_name VARCHAR(60),"
    print "    description  VARCHAR(120)"
    print ");"
    print "BEGIN TRANSACTION;" # vastly improves performance
}

{
    gsub(/'/, "''") # double up single quotes to escape them
    print "INSERT INTO Package_Descriptions"
    print "     VALUES ( '" $1 "', '" $2 "');"
}

END {
    print "COMMIT;"
}
```

Second, the `insert_package_files.awk` program:

```
#!/usr/bin/awk -f

# insert_package_files.awk - Insert records from
#                            package_files.tsv

BEGIN {
    FS="\t"
    print "DROP TABLE IF EXISTS Package_Files;"
    print "CREATE TABLE Package_Files ("
    print "    package_name VARCHAR(60),"
    print "    file         VARCHAR(120),"
    print "    size_bytes   INTEGER"
    print ");"
    print "BEGIN TRANSACTION;" # vastly improves performance
}

{
    gsub(/'/, "''") # double up single quotes to escape them
    print "INSERT INTO Package_Files"
    print "     VALUES ('" $1 "', '" $2 "','" $3 "');"
}

END {
    print "COMMIT;"
}
```

## Summing Up

SQL is an important and widely used technology. It’s kinda fun too. While we looked at the general features and characteristics of SQL, there is much more to learn. For example, there are the more advanced concepts such as *normalization*, *referential integrity*, and *relational algebra*. Though we didn’t get to the really heady stuff, we did cover enough to get some real work done whenever we need to integrate data storage into our scripts and projects.

## Further Reading

- *SQL Style Guide by Simon Holywell* The style guide that influenced the formatting of the SQL presented in this adventure. https://www.sqlstyle.guide/
- *SQL Tutorial* A good general tutorial on SQL. Covers most of the major SQL dialects (though not SQLite). https://www.w3schools.com/sql/
- *SQLite project home page* Includes detailed documentation. https://www.sqlite.org/index.html
- *SQLite Tutorial* An excellent detailed tutorial and reference for the SQLite dialect of SQL. Definitely worth checking out if you plan to use SQLite seriously. https://www.sqlitetutorial.net/
- *MySQL home page* A very popular multi-user database system used by many websites. https://mysql.com/
- *MariaDB Foundation home page* MariaDB is a fork of the open source version of MySQL. The project came about out of concern over Oracle’s purchase of the company behind MySQL. https://mariadb.org/
- *PostgreSQL project home page* Another very popular open source multi-user database. https://www.postgresql.org/
- Various Wikipedia articles providing background for the topics covered in this adventure:
  - *SQL* https://en.wikipedia.org/wiki/SQL
  - *Relational Database* https://en.wikipedia.org/wiki/Relational_database
  - *Relational Model* https://en.wikipedia.org/wiki/Relational_model
  - *LAMP Software Stack* https://en.wikipedia.org/wiki/LAMP_(software_bundle)
  - *MySQL* https://en.wikipedia.org/wiki/MySQL
  - *MariaDB* https://en.wikipedia.org/wiki/MariaDB
  - *PostgreSQL* https://en.wikipedia.org/wiki/PostgreSQL
- Stanford University offers an online course called “Introduction to Databases” taught by Professor Jennifer Widom. I took this course; it’s quite good. The course videos are available in this YouTube playlist: https://www.youtube.com/playlist?list=PLroEs25KGvwzmvIxYHRhoGTz9w8LeXek0
- Finally, the phrase “gird your grid for a big one” is taken from the 1971 comedy album *I Think We’re All Bozos on This Bus* by the Firesign Theater. Besides being very funny to retro-futurism fans like myself, the album is notable because it contains one of the first pop culture references to computer hacking. You can read more about it at Wikipedia: [https://en.wikipedia.org/wiki/I_Think_We%27re_All_Bozos_on_This_Bus](https://en.wikipedia.org/wiki/I_Think_We're_All_Bozos_on_This_Bus)