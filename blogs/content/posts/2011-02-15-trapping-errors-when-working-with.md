---
title: Trapping errors when working with linked servers
author: SQLDenis
type: post
date: 2011-02-15T15:07:00+00:00
ID: 1044
excerpt: "I have a bunch of linked servers to SQL Servers and also to Sybase ASE Servers ON AIX machines. There are some interesting things that can happen. For example if you type the name of the object or the linked server itself wrong, you can't trap this.....&hellip;"
url: /index.php/datamgmt/datadesign/trapping-errors-when-working-with/
views:
  - 14824
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - errors
  - how to
  - linked servers
  - trapping

---
I have a bunch of linked servers to SQL Servers and also to Sybase ASE Servers ON AIX machines. There are some interesting things that can happen. For example if you type the name of the object or the linked server itself wrong, you can't trap this..... or can you?

Let's take a look. Open up SSMS and connect to your local instance. Now create a linked server named TestLinkedServer which points to the local server. The code to do that is below.

sql
EXEC master.dbo.SP_ADDLINKEDSERVER @server = N'TestLinkedServer',
                                   @srvproduct=N'',
                                   @datasrc='(local)',
                                   @provider='SQLNCLI'
```

Now we can do a small test, run the following code

sql
SELECT * FROM OPENQUERY(TestLinkedServer,'select count(*) from tempdb..sysobjects')
```

That should return an integer.

If we try something more interesting like a division by zero, will it get trapped?

sql
BEGIN TRY	
	 SELECT * FROM OPENQUERY(TestLinkedServer,'select 1/0')
END TRY

BEGIN CATCH
	 
	PRINT ERROR_MESSAGE() 
	PRINT  ERROR_NUMBER()
END CATCH
PRINT 'TEST'
```

_Divide by zero error encountered.
  
8134
  
TEST_

Yes, that worked just as expected.
  
Now what do you think will happen if we change the table name from sysobjects to sysobjects2? Let's run it and see

sql
BEGIN TRY	
	 SELECT * FROM OPENQUERY(TestLinkedServer,'select count(*) from tempdb..sysobjects2')
END TRY

BEGIN CATCH
	 
	PRINT ERROR_MESSAGE() 
	PRINT  ERROR_NUMBER()
END CATCH
PRINT 'TEST'
```

_OLE DB provider “SQLNCLI10” for linked server “TestLinkedServer” returned message “Deferred prepare could not be completed.”.
  
Msg 8180, Level 16, State 1, Line 1
  
Statement(s) could not be prepared.
  
Msg 208, Level 16, State 1, Line 1
  
Invalid object name 'tempdb..sysobjects2'._

Ouch it blew up on us and never made it to the print statement.

How about if we use TestLinkedServer2 instead of TestLinkedServer?

sql
BEGIN TRY	
	 SELECT * FROM OPENQUERY(TestLinkedServer2,'select count(*) from tempdb..sysobjects')
END TRY

BEGIN CATCH
	 
	PRINT ERROR_MESSAGE() 
	PRINT  ERROR_NUMBER()
END CATCH
PRINT 'TEST'
```

_Msg 7202, Level 11, State 2, Line 4
  
Could not find server 'TestLinkedServer2' in sys.servers. Verify that the correct server name was specified. If necessary, execute the stored procedure sp_addlinkedserver to add the server to sys.servers._

Same problem, blows up and never makes it to the print statement. What if we take the last two examples and wrap them inside an exec statement?

sql
BEGIN TRY	
	 exec( 'SELECT * FROM OPENQUERY(TestLinkedServer,''select count(*) from tempdb..sysobjects2'')')
END TRY

BEGIN CATCH
	 
	PRINT ERROR_MESSAGE() 
	PRINT  ERROR_NUMBER()
END CATCH
PRINT 'TEST'
```

_OLE DB provider “SQLNCLI10” for linked server “TestLinkedServer” returned message “Deferred prepare could not be completed.”.
  
Invalid object name 'tempdb..sysobjects2'.
  
208
  
TEST_

That caught the exception and TEST was printed

sql
BEGIN TRY	
	 exec( 'SELECT * FROM OPENQUERY(TestLinkedServer2,''select count(*) from tempdb..sysobjects'')')
END TRY

BEGIN CATCH
	 
	PRINT ERROR_MESSAGE() 
	PRINT  ERROR_NUMBER()
END CATCH
PRINT 'TEST'
```

_Could not find server 'TestLinkedServer2' in sys.servers. Verify that the correct server name was specified. If necessary, execute the stored procedure sp_addlinkedserver to add the server to sys.servers.
  
7202
  
TEST_

Beautiful, Even using a non existent linked server name is caught when using it inside an EXEC statement.

As you can see it is possible to trap a problem with linked servers if you wrap it inside an Exec statement which you could not trap otherwise. One thing you can't trap however is a timeout.

Here are some of the error messages I trapped on my boxes

Cannot fetch a row from OLE DB provider “MSDASQL” for linked server “Test”.
  
Cannot initialize the data source object of OLE DB provider “MSDASQL” for linked server “Test”.
  
The OLE DB provider “MSDASQL” for linked server “Test” could not INSERT INTO table “[MSDASQL]”.
  
The OLE DB provider “MSDASQL” for linked server “Test” reported an error committing the current transaction.

Hope you learned something and hopefully this will help you in your troubles with linked servers

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22