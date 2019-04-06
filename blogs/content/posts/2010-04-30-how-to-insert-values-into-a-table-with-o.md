---
title: How to insert values into a table with only an identity column
author: SQLDenis
type: post
date: 2010-04-30T14:55:19+00:00
url: /index.php/datamgmt/dbprogramming/how-to-insert-values-into-a-table-with-o/
views:
  - 7017
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - how to
  - identity
  - tip

---
How to insert values into a table with only an identity column

Some databases have something called a sequence, you can use this to generate values like an identity but it can be used accross many tables. In SQL Server you could simulate that by creating a table that has an identity column, inserting into that table and then use the identity generated in your other tables
  
The reason I am writing this is because I saw the following table

<pre>CREATE TABLE Sequence (ID int identity not null primary key,Dummy tinyint)
GO</pre>

I was thinking to myself why they have that dummy value, so I spoke to one of these people who were using the system. The reason they have this dummy value is so that they can insert into this table like this

<pre>INSERT INTO Sequence VALUES(1)
INSERT INTO Sequence VALUES(1)
INSERT INTO Sequence VALUES(1)</pre>

And now when you query the table

<pre>SELECT ID 
FROM  Sequence </pre>

You get these 3 rows as output

<pre>ID
1
2
3</pre>

So the dummy value is there so that they can insert into the table. Even though the dummy column is not big it still takes up space and it was also nullable which also takes up some space.
  
Let&#8217;s do this a different way. First drop the table we created before.

<pre>DROP TABLE Sequence</pre>

Now create the table like this; without the dummy value

<pre>CREATE TABLE Sequence (ID int identity not null primary key)
GO</pre>

Here is how the insert looks like if you only have the identity column

<pre>INSERT INTO Sequence DEFAULT VALUES
INSERT INTO Sequence DEFAULT VALUES
INSERT INTO Sequence DEFAULT VALUES
INSERT INTO Sequence DEFAULT VALUES</pre>

All you need to use is default values in the insert statements, it will then generate the identity

<pre>SELECT ID 
FROM  Sequence </pre>

<pre>ID
1
2
3
4</pre>

Let&#8217;s take a look at another example, what if we had two other columns and they had defaults on them? First create this table.

<pre>CREATE TABLE Sequence2 (ID int identity not null primary key, 
			Somedate datetime default getdate() not null,
			SomeID int default 0 not null)
GO</pre>

Now run these statements

<pre>INSERT INTO Sequence2 DEFAULT VALUES
INSERT INTO Sequence2 DEFAULT VALUES
INSERT INTO Sequence2 DEFAULT VALUES
INSERT INTO Sequence2 DEFAULT VALUES</pre>

Now let&#8217;s look what is in the table

<pre>SELECT * FROM  Sequence2</pre>

<pre>ID	Somedate	SomeID
1	2010-04-30 12:50:14.693	0
2	2010-04-30 12:50:14.693	0
3	2010-04-30 12:50:14.693	0
4	2010-04-30 12:50:14.693	0</pre>

As you can see it used the default values for the columns with defaults and also created the identity values.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22