---
title: how to make sure that the view will have the underlying table changes by using sp_refreshview
author: SQLDenis
type: post
date: 2009-01-28T14:32:44+00:00
url: /index.php/datamgmt/datadesign/how-to-make-sure-that-the-view-will-have/
views:
  - 9030
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - how to
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip
  - trick
  - views

---
**sp_refreshview or how to make sure that the view will have the underlying table changes**

Got a question about this on our [Microsoft SQL Server Programming Forum][1] so you know it is time for a quick blog post.

Did you know that when you create a view and then later change the table the view is not updated?
  
let me show you what I mean.
  
Run the following block of code

<pre>CREATE TABLE TestTable (id INT,SomeCol VARCHAR(666))
GO

INSERT TestTable VALUES(1,'ABC')
GO

SELECT * FROM TestTable
GO

CREATE VIEW TestView
AS
SELECT * FROM TestTable
GO

SELECT * FROM TestView
GO</pre>

Now we will change that table by adding another column

<pre>ALTER TABLE TestTable
ADD Col2 DATETIME DEFAULT CURRENT_TIMESTAMP
GO

INSERT TestTable(id,SomeCol) VALUES(2,'XYZ')
GO</pre>

Now run the selects again

<pre>SELECT * FROM TestTable
GO

SELECT * FROM TestView
GO</pre>

See what happened? The TestView does not include the Col2 column. So what can you do? There are at least two things that you can do. You can recreate the view with a create or alter statement or you can use sp_refreshview, run the code below to see how that works

<pre>sp_refreshview TestView
GO

--All good now
SELECT * FROM TestView
GO


--Clean up this mess--
DROP VIEW TestView
GO


DROP TABLE TestTable
GO</pre>

And yes I know &#8216;real&#8217; SQL programmers never use SELECT * and &#8216;real&#8217; SQL programmers name their defaults 😉



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22