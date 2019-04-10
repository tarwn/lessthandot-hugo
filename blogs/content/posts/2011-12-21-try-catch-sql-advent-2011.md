---
title: 'SQL Advent 2011 Day 21: TRY CATCH'
author: SQLDenis
type: post
date: 2011-12-21T23:15:00+00:00
ID: 1458
excerpt: Today we are going to take a look at TRY CATCH. Like in most modern programming languages, you put your code in the TRY block and you check for the errors in the CATCH block. SQL Server has a bunch of functions that will help you identify why your code failed, here is a list of the functions and what they return
url: /index.php/datamgmt/datadesign/try-catch-sql-advent-2011/
views:
  - 5293
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - error
  - error handling
  - sql server 2005
  - sql server 2008
  - try catch

---
In my [Are you ready for SQL Server 2012 or are you still partying like it is 1999?][1] post, I wrote about how you should start using SQL Server 2005 and SQL Server 2008 functionality now in order to prepare for SQL Server 2012. I still see tons of code that is written in the pre 2005 style and people still keep using those functions, procs and statements even though SQL Server 2005 and 2008 have much better functionality.

Today we are going to take a look at TRY CATCH. Like in most modern programming languages, you put your code in the TRY block and you check for the errors in the CATCH block. SQL Server has a bunch of functions that will help you identify why your code failed, here is a list of the functions and what they return

**ERROR_NUMBER()** 
  
returns the number of the error

**ERROR_SEVERITY()** 
  
returns the severity of the error

**ERROR_STATE()**
  
returns the error state number

**ERROR_PROCEDURE()**
  
returns the name of the stored procedure or trigger where the error occurred, this will be NULL if you run an ad-hoc SQL statement

**ERROR_LINE()**
  
returns the line number inside the routine that caused the error

**ERROR_MESSAGE()**
  
returns the complete text of the error message. The text includes the values supplied for any substitutable parameters, such as lengths, object names, or times

Let's run an example that generates a divide by zero error, in the catch we are just doing a simple select that calls the functions mentioned before to see what they return

sql
BEGIN TRY
    --  divide-by-zero error.
    SELECT 1/0
END TRY
BEGIN CATCH
    SELECT
    ERROR_NUMBER() AS ErrorNumber
    ,ERROR_SEVERITY() AS ErrorSeverity
    ,ERROR_STATE() AS ErrorState
    ,ERROR_PROCEDURE() AS ErrorProcedure
    ,ERROR_LINE() AS ErrorLine
    ,ERROR_MESSAGE() AS ErrorMessage;
END CATCH;
```



<pre>ErrorNumber	ErrorSeverity	ErrorState	ErrorProcedure	ErrorLine	ErrorMessage
8134	        16	        1	        NULL	        3	       Divide by zero 
                                                                               error encountered.</pre>



As you can see we got all that information back, that was pretty nice. Let's take it to the next step

Create the following table to store all the error information in

sql
CREATE TABLE LogErrors (ErrorTime datetime,
			ErrorNumber int,
			ErrorSeverity int,
			ErrorState int, 
			ErrorProc nvarchar(100), 
			ErrorLine int, 
			ErrorMessage nvarchar(1000))
GO
```

Create this stored procedure that will insert into the table we just created

sql
CREATE PROCEDURE prInsertError
AS
INSERT LogErrors
SELECT GETDATE(),
    ERROR_NUMBER(),
    ERROR_SEVERITY(), 
    ERROR_STATE(), 
    ERROR_PROCEDURE(), 
    ERROR_LINE(), 
    ERROR_MESSAGE() ;
GO
```

Run these 3 queries, they will generate 3 inserts into the LogErrors table

sql
BEGIN TRY
    SELECT 1/0
END TRY
BEGIN CATCH
    EXEC prInsertError
END CATCH;


BEGIN TRY
    SELECT convert(int,'a')
END TRY
BEGIN CATCH
    EXEC prInsertError
END CATCH;


BEGIN TRY
    SELECT convert(tinyint,300)
END TRY
BEGIN CATCH
    EXEC prInsertError
END CATCH;
```
If you check now what is in the table, you will see 3 rows

sql
SELECT * FROM LogErrors
```



<div class="tables">
  <table>
    <tr>
      <th>
        ErrorTime
      </th>
      
      <th>
        ErrorNumber
      </th>
      
      <th>
        ErrorSeverity
      </th>
      
      <th>
        ErrorState
      </th>
      
      <th>
        ErrorProc
      </th>
      
      <th>
        ErrorLine
      </th>
      
      <th>
        ErrorMessage
      </th>
    </tr>
    
    <tr>
      <td>
        2011-12-21 20:08:20.890
      </td>
      
      <td>
        8134
      </td>
      
      <td>
        16
      </td>
      
      <td>
        1
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        2
      </td>
      
      <td>
        Divide by zero error encountered.
      </td>
    </tr>
    
    <tr>
      <td>
        2011-12-21 20:08:22.907
      </td>
      
      <td>
        245
      </td>
      
      <td>
        16
      </td>
      
      <td>
        1
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        2
      </td>
      
      <td>
        Conversion failed when converting the varchar value 'a' to data type int.
      </td>
    </tr>
    
    <tr>
      <td>
        2011-12-21 20:08:25.270
      </td>
      
      <td>
        220
      </td>
      
      <td>
        16
      </td>
      
      <td>
        2
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        2
      </td>
      
      <td>
        Arithmetic overflow error for data type tinyint, value = 300.
      </td>
    </tr>
  </table>
</div>

That is all for today, as you can see error handling is much better than having to check for @@ERROR after every insert. You can also have 1 stored proc that you can call from everywhere and this proc will log all errors plus any other information you want to capture like user name, host name etc etc

Come back tomorrow for the next post in this series

 [1]: /index.php/DataMgmt/DataDesign/are-you-ready-for-sql