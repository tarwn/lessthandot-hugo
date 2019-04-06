---
title: Use DDL Triggers To Track Table Changes
author: SQLDenis
type: post
date: 2011-04-21T07:28:00+00:00
excerpt: "Someone wanted to know when and by who a certain table was dropped, I told the person that you can do this with a DDL trigger.Wouldn't it be nice if you could track exactly all the DDL statements that were executed on a table in your database? Well, you can by using DDL Triggers"
url: /index.php/datamgmt/datadesign/use-ddl-triggers-to-track/
views:
  - 15792
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - alter table
  - auditing
  - ddl
  - ddl triggers
  - triggers

---
Someone wanted to know when and by who a certain table was dropped, I told the person that you can do this with a DDL trigger.Wouldn&#8217;t it be nice if you could track exactly all the DDL statements that were executed on a table in your database? Well, you can by using DDL Triggers, DDL Triggers were added in SQL Server 2005. 

DDL triggers are a special kind of trigger that fire in response to Data Definition Language (DDL) statements. They can be used to perform administrative tasks in the database such as auditing and regulating database operations. A DDL trigger can be created on the database level or on the server level. In this post I will create a database level DDL trigger that will listen for the ALTER TABLE command.

First I will create a sample database

<pre>CREATE DATABASE TestTrigger
GO

USE TestTrigger
GO</pre>

Now I will create a table which will hold the DDL statement, the time and the login of the person who executed the statement

<pre>CREATE TABLE TriggerLog(DDL VARCHAR(300), ExecutedBy VARCHAR(100), EventDate datetime)
GO</pre>

Here is what my DDL trigger will look like, more information about DDL triggers can be found here: http://msdn.microsoft.com/en-us/library/ms189799.aspx

<pre>CREATE TRIGGER trALterTable 
ON DATABASE -- A DB level trigger
FOR ALTER_TABLE --Event we want to capture
AS 
  INSERT TriggerLog
  SELECT EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]','nvarchar(max)'), 
		COALESCE(SUSER_SNAME(),USER_NAME()), 
		GETDATE();
GO</pre>

The code in the trigger should be pretty simple to follow. The line _EVENTDATA().value(&#8216;(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]&#8217;,&#8217;nvarchar(max)&#8217;)_ is grabbing the DDL statement, more about EVENTDATA() can be found here: http://msdn.microsoft.com/en-us/library/ms173781.aspx

Next up is the test table that we will use to play around with

<pre>CREATE TABLE test(id INT)</pre>

The following block of code will add a column, change the data type of the column and will finally drop the column

<pre>ALTER TABLE test
ADD SomeDate date
GO

ALTER TABLE test
ALTER COLUMN SomeDate datetime2
GO


ALTER TABLE test
DROP COLUMN SomeDate 
GO</pre>

Now let&#8217;s see what we have in our log table

<pre>SELECT * FROM TriggerLog
order by EventDate</pre>

Here is the output
  


<div class="tables">
  <table>
    <tr>
      <th>
        DDL
      </th>
      
      <th>
        ExecutedBy
      </th>
      
      <th>
        EventDate
      </th>
    </tr>
    
    <tr>
      <td>
        ALTER TABLE test ADD SomeDate date
      </td>
      
      <td>
        Denis-PCDenis
      </td>
      
      <td>
        2011-04-19 20:18:07.590
      </td>
    </tr>
    
    <tr>
      <td>
        ALTER TABLE test ALTER COLUMN SomeDate datetime2
      </td>
      
      <td>
        Denis-PCDenis
      </td>
      
      <td>
        2011-04-19 20:18:09.900
      </td>
    </tr>
    
    <tr>
      <td>
        ALTER TABLE test DROP COLUMN SomeDate
      </td>
      
      <td>
        Denis-PCDenis
      </td>
      
      <td>
        2011-04-19 20:18:11.610
      </td>
    </tr>
    
    <table>
    </table>
  </table>
</div>

As you can see we have all the DDL statements captured in the table, the time it happened and the person who did it.
  
Let&#8217;s just drop and recreate the table

<pre>drop table Test
GO</pre>

Now create the table again

<pre>CREATE TABLE test(id INT)
GO</pre>

If you now execute this query, you will get back pretty much all the DDL statements that we executed before

<pre>SELECT DDL + 'GO'
FROM TriggerLog
ORDER BY EventDate</pre>

Here is what it looks like if you copied the results and pasted them into a query window.

<pre>ALTER TABLE test
ADD SomeDate date
GO
ALTER TABLE test
ALTER COLUMN SomeDate datetime2
GO
ALTER TABLE test
DROP COLUMN SomeDate 
GO</pre>

This was just a small example of how a DDL trigger works, A DDL trigger enables you to also not allow ALTER TABLE statements during business hours or for certain user even though they are db owner.

To see all the events that DDL triggers can listen for you can use the sys.trigger\_event\_types Object Catalog View

<pre>select type_name from sys.trigger_event_types </pre>

Here is a partial result set

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
CREATE_TABLE
  
ALTER_TABLE
  
DROP_TABLE
  
CREATE_INDEX
  
ALTER_INDEX
  
DROP_INDEX
  
CREATE_STATISTICS
  
UPDATE_STATISTICS
  
DROP_STATISTICS
  
CREATE_SYNONYM
  
DROP_SYNONYM
  
CREATE_VIEW
  
ALTER_VIEW
  
DROP_VIEW
  
CREATE_PROCEDURE

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22