---
title: How to use T-SQL to get the command line startup parameters that were used to start SQL Server
author: SQLDenis
type: post
date: 2010-05-18T15:04:07+00:00
ID: 792
url: /index.php/datamgmt/datadesign/how-to-use-t-sql-to-get-the-command-line/
views:
  - 6592
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - howto
  - sql server 2005
  - sql server 2008
  - tip

---
This is just a quick blogpost that will show you how you can use T-SQL to get the command line startup parameters that were used to start SQL Server. Before I start I want to warn you that you **do not try NET START and NET STOP on a production server since you might mess stuff up big time!!**.

In order to start SQL Server with parameters we can use the configuration tool or we can use the command line, of course we will use the command line

I advise you to read the [Using the Command Line to manage SQL Server services][1] wiki article first before continuing.
  
First we are going to start SQL Server with the -c parameter

## Starting SQL Server with one startup parameter

_-c
  
Shortens startup time when starting SQL Server from the command prompt. Typically, the SQL Server Database Engine starts as a service by calling the Service Control Manager. Because the SQL Server Database Engine does not start as a service when starting from the command prompt, use -c to skip this step._

If your SQL Server is running then you need to shut it down first. You can either do it from SSMS, the service or from a command line like this: _NET STOP MSSQLSERVER_ 

Now it is time to start up SQL Server with the -c parameter, here is how you do that, open a command prompt and type _NET START MSSQLSERVER /c_
  
Your output should look like this

The SQL Server (MSSQLSERVER) service is starting.
  
The SQL Server (MSSQLSERVER) service was started successfully.

Now we can use the undocumented sp_readerrorlog proc to see what we started with, you can also just open up your error log of course.
  
More info about how to use sp_readerrorlog is available on our wiki here: [Read the error log with T-SQL][2]

To search the current error log we can do this

sql
EXEC sp_readerrorlog 0, 1, 'Command Line Startup Parameters'
```

Here is the output
  
2010-05-18 12:36:51.010 Server Command Line Startup Parameters: /c

You can also use 2 search arguments like this

sql
EXEC sp_readerrorlog 0, 1, 'Command Line Startup Parameters','/c'
```

Output is the same
  
2010-05-18 12:36:51.010 Server Command Line Startup Parameters: /c

Now, let's stop the SQL Server instance, type this in a command window: _NET STOP MSSQLSERVER_ 

Your output should look like this 

<pre>The SQL Server (MSSQLSERVER) service is stopping.
The SQL Server (MSSQLSERVER) service was stopped successfully.</pre>

## Starting SQL Server with two startup parameters

I am adding another parameter, this time I will add the -g parameter

_-g
  
Specifies an integer number of megabytes (MB) of memory that SQL Server will leave available for memory allocations within the SQL Server process, but outside the SQL Server memory pool. The memory outside of the memory pool is the area used by SQL Server for loading items such as extended procedure .dll files, the OLE DB providers referenced by distributed queries, and automation objects referenced in Transact-SQL statements. The default is 256 MB._

Type this in a command window to start SQL Server with both the c and the g startup parameter: _NET START MSSQLSERVER /c /g 5000_

Your output should look like this 

<pre>The SQL Server (MSSQLSERVER) service is starting.
The SQL Server (MSSQLSERVER) service was started successfully.</pre>

Now let's run the same stored procedure from before

sql
EXEC sp_readerrorlog 0, 1, 'Command Line Startup Parameters'
```

And here is the output
  
2010-05-18 12:37:54.820 Server Command Line Startup Parameters: /c /g

Now you can also search for /c or /g and the result is the same as before
  
Search for /c

sql
EXEC sp_readerrorlog 0, 1, 'Command Line Startup Parameters','/c'
```

Search for /g

sql
EXEC sp_readerrorlog 0, 1, 'Command Line Startup Parameters','/g'
```

So there you have it EXEC sp_readerrorlog 0, 1, 'Command Line Startup Parameters' is a quick way to check how SQL Server was started without looking through your error log

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://wiki.ltd.local/index.php/Using_the_Command_Line_to_manage_SQL_Server_services
 [2]: http://wiki.ltd.local/index.php/Read_the_error_log_with_T-SQL
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22