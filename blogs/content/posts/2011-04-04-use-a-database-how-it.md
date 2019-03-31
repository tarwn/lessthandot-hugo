---
title: Use a database how it was intended to be used
author: SQLDenis
type: post
date: 2011-04-04T23:40:00+00:00
excerpt: |
  Take a look at this piece of junk code, what pops up in your head when you look at this
  CREATE FUNCTION [dbo].[age](@set varchar(10))
  RETURNS TABLE
  AS
  BEGIN
      IF  (@set = 'tall')
           SELECT * from player where height &gt; 180
      ELSE IF (&hellip;
url: /index.php/datamgmt/datadesign/use-a-database-how-it/
views:
  - 4672
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - design
  - normalization

---
Take a look at this piece of <del>junk</del> code, what pops up in your head when you look at this?

<pre>CREATE FUNCTION [dbo].[age](@set varchar(10))
RETURNS TABLE
AS
BEGIN
    IF  (@set = 'tall')
         SELECT * from player where height &gt; 180
    ELSE IF (@set = 'average')
         SELECT * from player where height &gt;= 155 and height &lt;=175
    ELSE IF (@set = 'low')
         SELECT * from player where height &lt; 155
END</pre>

If you are thinking _silly you, you can&#8217;t have an IF statement like that in a function_, then you have disappointed me. What you really should be saying is the following: [why are you hardcoding this, create a heights table and then grab all the heights that are valid for the range][1]

Yes, I grabbed this example from the following question: [TSQL &#8211; If..Else statement inside Table-Valued Functions &#8211; cant go through…][2]. As you can see several people answered with how to use a CASE statement instead. That is all nice and dandy but the information should be in a table instead

# Single Source of Truth

The thing that is wrong with that piece of code posted is not the fact that the IF statement doesn&#8217;t work but the fact that a table was not used to store the info. If you are using a table then you can populate a drop down in a form, use the table in the function, in other procedures, views etc etc. If you need to make a change, you do it in one place and one place only. 

This is a very important thing to understand, if you have the same information from 10 different sources all over the place hen when you need to make a change you are bound to make mistakes. Can you imagine if you need to store tax information, having this hardcoded like that? That is insanity.

It is not difficult to create such a table.
  
Here is a sample table

<pre>CREATE TABLE Heights (	
	HeightId int primary key not null,
	HeightDescription varchar(20) not null,
	StartRange smallint not null,
	EndRange smallint not null)
GO</pre>

Now let&#8217;s insert some data

<pre>INSERT Heights values(1,'Small',0,154)
INSERT Heights values(2,'Average',155,175)
INSERT Heights values(3,'Tall',176,300)</pre>

Now if I want to know if 181 centimeters is considered tall or not, I can run this

<pre>SELECT HeightDescription
FROM Heights
WHERE 181 between StartRange and EndRange</pre>

Let&#8217;s continue by adding another table, this table will have some people and their height. 

<pre>CREATE TABLE Players(Player varchar(200),Height smallint)
GO</pre>

The following people are the shortest and tallest people on record

<pre>INSERT Players values('Robert Wadlow',272)
INSERT Players values('John Rogan',267)
INSERT Players values('June Rey Balawing',56)
INSERT Players values('Gul Mohammed',57)
INSERT Players values('Pauline Musters',58)</pre>

Now when I want to list all the tall people, my query looks like this

<pre>SELECT p.* 
FROM Players p 
JOIN Heights h on p.Height between h.StartRange and h.EndRange
where h.HeightDescription = 'Tall'</pre>

When I want to list all the tall people, my query looks like this

<pre>SELECT p.* 
FROM Players p 
JOIN Heights h on p.Height between h.StartRange and h.EndRange
where h.HeightDescription = 'Small'</pre>

If tomorrow I decide that tall people have to be taller than 180 centimeters, I only have to change this in 1 place. This is much cleaner and also easily maintainable.

P.S.
  
Yes, I know, instead of having a player column I should have at least first and last name columns (preferably in a person table) but in the day and age of internet induced ADD I didn&#8217;t feel it would add anything to the point I was trying to make in this post&#8230;&#8230;so forgive me

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/5544269/tsql-if-else-statement-inside-table-valued-functions-cant-go-through/5544320#5544320
 [2]: http://stackoverflow.com/questions/5544269/tsql-if-else-statement-inside-table-valued-functions-cant-go-through
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22