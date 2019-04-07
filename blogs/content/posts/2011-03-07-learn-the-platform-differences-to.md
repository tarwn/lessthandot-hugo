---
title: Learn the platform differences to be more effective
author: SQLDenis
type: post
date: 2011-03-07T21:03:00+00:00
ID: 1070
excerpt: 'Something that you are used to doing on one platform is not always the best thing on another platform. Compacting databases might be good in Access, shrinking databases is not so good in SQL Server. Looping in SQL Server instead of using a SET based sol&hellip;'
url: /index.php/datamgmt/datadesign/learn-the-platform-differences-to/
views:
  - 2989
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - alter table
  - columns
  - sql server 2005
  - sql server 2008
  - tip

---
Something that you are used to doing on one platform is not always the best thing on another platform. Compacting databases might be good in Access, shrinking databases is not so good in SQL Server. Looping in SQL Server instead of using a SET based solution is another mistake that some procedural programmers make.

A couple of minutes ago I saw this question on StackOverflow: [SQL Server equivalent of MySQL Dump to produce insert statements for all data in a table][1] 

The person wanted the following

> I have an application that uses a SQL Server database with several instances of the database&#8230;test, prod, etc&#8230; I am making some application changes and one of the changes involves changing a column from a nvarchar(max) to a nvarchar(200) so that I can add a unique constraint on it. SQL Server tells me that this requires dropping the table and recreating it.
> 
> I want to put together a script that will do the table drop, recreate it with the new schema, and then reinsert the data that was there previously all in one go, if possible, just to keep things simple for use when I migrate this change to production.
> 
> There is probably a good SQL Server way to do this but I&#8217;m just not aware of it. If I was using Mysql I would mysqldump the table and its contents, and use that as my script for applying that change to production. I can&#8217;t find any export functionality in SQL server that will give me a text file consisting of inserts for all data in a table.

Of course I could have told him/her to use the scripting functionality in SQL Server or even use the scripting functionality in SSMS Toolpack. But there is of course a better (my opinion) way

Let&#8217;s take a look

If I have this table

sql
CREATE TABLE Test(SomeColumn NVARCHAR(MAX), ID INT, SomeDate DATETIME)
GO
```

I add 1 row

sql
INSERT Test VALUES(REPLICATE('T',200),1,GETDATE())
```

Now I want to create this index

sql
CREATE INDEX ix_test ON Test(ID,SomeColumn)
```

Here is the error that is raised

<div style="border:1px solid black;background-color:#444;color:white;margin:0 20px;padding:0 5px 0 5px;">
  Msg 1919, Level 16, State 1, Line 1<br /> Column &#8216;SomeColumn&#8217; in table &#8216;Test&#8217; is of a type that is invalid for use as a key column in an index.
</div>

Now, let&#8217;s change the column to nvarchar(200). You can do this by using the ALTER TABLE&#8230;ALTER COLUMN syntax. Here is what it looks like for this table

sql
ALTER TABLE Test ALTER COLUMN SomeColumn NVARCHAR(200)
GO
```

Now we have no problem creating this index

sql
CREATE INDEX ix_test ON Test(ID,SomeColumn)
```

You can verify that the select works

sql
SELECT * FROM Test
```

Now, let&#8217;s drop the table and take a look at what happens when the length of the data is more than the column size we want
  
Drop the table

sql
DROP TABLE test
```

create the table again with one row, this time we have 300 bytes in the column

sql
CREATE TABLE Test(SomeColumn NVARCHAR(MAX), ID INT, SomeDate DATETIME)
GO

INSERT Test VALUES(REPLICATE('T',300),1,GETDATE())
```

Now when we try to make in NVARCHAR(200)

sql
ALTER TABLE Test ALTER COLUMN SomeColumn NVARCHAR(200)
GO
```

We get the infamous error _String or binary data would be truncated._

<div style="border:1px solid black;background-color:#444;color:white;margin:0 20px;padding:0 5px 0 5px;">
  Msg 8152, Level 16, State 10, Line 1<br /> String or binary data would be truncated.<br /> The statement has been terminated.
</div>

So first you need to find out what the max length is of the column and adjust your new size that you want to change the column size to.

On the stackoverflow question I gave an additional answer, you can also select into a new table, doing the conversion upon selection and then drop the old table and rename the table just created.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/5225923/sql-server-equivalent-of-mysql-dump-to-produce-insert-statements-for-all-data-in
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22