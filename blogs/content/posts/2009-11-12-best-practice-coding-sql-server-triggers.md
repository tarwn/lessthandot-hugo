---
title: 'Best Practice: Coding SQL Server triggers for multi-row operations'
author: SQLDenis
type: post
date: 2009-11-12T18:12:21+00:00
excerpt: |
  Best Practice: coding SQL Server triggers for multi-row operations
  
  There are many forum posts where people code triggers but these triggers are coded incorrectly because they don't account for multi-row operations. A trigger fires per batch not per r&hellip;
url: /index.php/datamgmt/datadesign/best-practice-coding-sql-server-triggers/
views:
  - 44739
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - how to
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip
  - triggers

---
Best Practice: coding SQL Server triggers for multi-row operations

There are many forum posts where people code triggers but these triggers are coded incorrectly because they don&#8217;t account for multi-row operations. A trigger fires per batch not per row, if you are lucky you will get an error&#8230;if you are not lucky you will not get an error but it might take a while before you notice that you are missing a whole bunch of data
  
Let&#8217;s take a look, first create these two tables

<pre>create table Test(id int identity not null primary key, 
			SomeDate datetime not null)
GO

create table TestHistory(id int  not null, 
			InsertedDate datetime not null)
GO</pre>

Now create this trigger, this trigger is very simple, it basically inserts a row into the history table every time an insert happens in the test table

<pre>CREATE  TRIGGER trTest
    ON Test
    FOR INSERT
    AS
     
    IF @@ROWCOUNT =0
    RETURN
     
    DECLARE @id int
    SET @id = (SELECT id 
    FROM inserted)
    
    INSERT TestHistory (id,InsertedDate)
    SELECT @id, getdate()
    
    GO</pre>

Run this insert statement which only inserts one row

<pre>insert Test(SomeDate) values(getdate())</pre>

Now run this to see what is in the history table

<pre>select * from TestHistory</pre>

<pre>1	2009-11-12 14:51:21.103</pre>

That all works fine, what happens when we try to insert 2 rows?

<pre>insert Test(SomeDate)
select getdate()
union all
select getdate() + 1</pre>

Here is the error.

_Server: Msg 512, Level 16, State 1, Procedure trTest, Line 11
  
Subquery returned more than 1 value. This is not permitted when the subquery follows =, !=, <, <= , >, >= or when the subquery is used as an expression.
  
The statement has been terminated._

What would happen if you coded the trigger in this way

<pre>ALTER TRIGGER trTest
    ON Test
    FOR INSERT
    AS
     
    IF @@ROWCOUNT =0
    RETURN
     
    DECLARE @id int
    SELECT @id = id 
    FROM inserted
    
    INSERT TestHistory (id,InsertedDate)
    SELECT @id, getdate()
    
    GO</pre>

Now insert one row

<pre>insert Test(SomeDate) values(getdate())</pre>

We look again what is in the history table, as you can see we have id 1 and 4, this is because id 2 and 3 failed and were rolled back

<pre>select * from TestHistory</pre>

<pre>1	2009-11-12 14:51:21.103
4	2009-11-12 14:52:08.370</pre>

Here is where it gets interesting, run this code

<pre>insert Test(SomeDate)
select getdate()
union all
select getdate() + 1</pre>

That runs fine but when we look now we are missing row 5 in the history table

<pre>select * from TestHistory</pre>

<pre>1	2009-11-12 14:51:21.103
4	2009-11-12 14:52:08.370
6	2009-11-12 14:52:20.167</pre>

let&#8217;s try that again

<pre>insert Test(SomeDate)
select getdate()
union all
select getdate() + 1</pre>

Now we are missing row 7 in the history table

<pre>select * from TestHistory</pre>

<pre>1	2009-11-12 14:51:21.103
4	2009-11-12 14:52:08.370
6	2009-11-12 14:52:20.167
8	2009-11-12 14:52:38.917</pre>

The problem is with this line of code

<pre>SELECT @id = id FROM inserted</pre>

@id will only hold the value for the row that was returned last in the result set

Here is how you would change the trigger to work correctly

<pre>ALTER TRIGGER trTest
    ON Test
    FOR INSERT
    AS
     
    IF @@ROWCOUNT =0
    RETURN
     
        
    INSERT TestHistory (id,InsertedDate)
    SELECT id, getdate()
    FROM inserted
    
GO</pre>

Now run this

<pre>insert Test(SomeDate) values(getdate())</pre>

We can now verify that it works correctly

<pre>select * from TestHistory</pre>

<pre>1	2009-11-12 14:51:21.103
4	2009-11-12 14:52:08.370
6	2009-11-12 14:52:20.167
8	2009-11-12 14:52:38.917
9	2009-11-12 14:53:44.433</pre>

Now run this for 2 rows

<pre>insert Test(SomeDate)
select getdate()
union all
select getdate() + 1</pre>

And as you can see both rows were inserted into the history table

<pre>select * from TestHistory</pre>

<pre>1	2009-11-12 14:51:21.103
4	2009-11-12 14:52:08.370
6	2009-11-12 14:52:20.167
8	2009-11-12 14:52:38.917
9	2009-11-12 14:53:44.433
10	2009-11-12 14:53:51.870
11	2009-11-12 14:53:51.870</pre>

So what is worse in this case? The error message or the fact that the code didn&#8217;t blow up but that the insert wasn&#8217;t working correctly? I&#8217;ll take an error message any time over the other problem.

I am putting together a [SQL Server Best Programming Practices][1] wiki page, this blog post is part of it as are other posts and articles either from this site as well as from other sites. I am still working on the [SQL Server Best Programming Practices][1] wiki page but I encourage you to bookmark it and come back every now and then because I will be adding more content



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://wiki.ltd.local/index.php/SQL_Server_Programming_Best_Practices
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22