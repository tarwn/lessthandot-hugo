---
title: Understanding SQL Server 2000 Pivot with Aggregates
author: George Mastros (gmmastros)
type: post
date: 2011-03-08T13:45:00+00:00
ID: 1074
excerpt: "This month's T-SQL Tuesday is being hosted by our very own, Jes Borland (Twitter | Blog).  Not only is she hosting this month but she is making it possible for LessThanDot's first T-SQL Tuesday event.  The topic that is brought to us is to discuss with&hellip;"
url: /index.php/datamgmt/datadesign/understanding-sql-server-2000-pivot/
views:
  - 16631
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server

---
[<img src="/wp-content/uploads/blogs/DataMgmt/olap_1.gif" align="left" />][1]
  
This month&#8217;s T-SQL Tuesday is being hosted by our very own, Jes Borland ([Twitter][2] | [Blog][3]). Not only is she hosting this month but she is making it possible for LessThanDot&#8217;s first T-SQL Tuesday event. The topic that is brought to us is to discuss with everyone how we solved business problems with aggregate functions. I thought this would be a good time to explain how you can write a pivot query in SQL 2000 using aggregate functions. Writing a query is one thing, understanding how it works is another. The goal of this blog post is to explain HOW it works so that it can be applied to other interesting problems.

Why? This database engine is 11 years old, why bother? We are sometimes stuck dealing with an older database engine. Understanding the concept behind this pivot will also allow you to use the technique in other interesting ways. Personally, I think that by understanding how this works is another step in the process of “thinking in sets”.

When we think of pivoting data, we usually want to see several rows of data displayed as additional columns. Often times this is for reporting purposes or GUI displays within an application.

This blog is meant to explain the process of pivoting the data. Sure, many people know how to write a pivot query using aggregate functions, but how many people actually understand it from a set based perspective. Understanding what the code is doing will help us to apply the principals to other queries.

To explain this process, I will use a set of dummy data to aide in visualizing the data. I will also build up to the final query, but examining each step along the way.

The data:

<pre>Id          Name                 Value
----------- -------------------- --------------------
1           Name                 George
1           ShoeSize             9.5

2           Name                 Bill
2           ShoeSize             10.5

3           Name                 John
3           ShoeSize             9

4           Name                 Greg
4           ShoeSize             9
</pre>

The data is relatively simple, but is meant to demonstrate the concept. The goal of this blog is to explain how we can pivot the data shown above in to the following output.

<pre>Id          Name                 ShoeSize
----------- -------------------- --------------------
1           George               9.5
2           Bill                 10.5
3           John                 9
4           Greg                 9
</pre>

As you can see, the original data had the name and shoe size for each person on separate rows. The output has just a single row per person but with additional columns for the data.

Setting up the code&#8230;.

sql
Create Table #Temp(Id Int, Name VarChar(20), Value VarChar(20))

Insert Into #Temp Values(1, 'Name','George')
Insert Into #Temp Values(1, 'ShoeSize','9.5')

Insert Into #Temp Values(2, 'Name','Bill')
Insert Into #Temp Values(2, 'ShoeSize','10.5')

Insert Into #Temp Values(3, 'Name','John')
Insert Into #Temp Values(3, 'ShoeSize','9')

Insert Into #Temp Values(4, 'Name','Greg')
Insert Into #Temp Values(4, 'ShoeSize','9')
```

Our first query will simply show the data.

sql
Select *
From   #Temp
```

Simple. We see all the data in the table. For our next step, let&#8217;s set up the output structure. We know we want to see the ID column and the Value column twice, once for the person&#8217;s name and again for the shoe size. 

sql
Select Id,
       Value,
       Value
From   #Temp
```

<pre>Id          Value                Value
----------- -------------------- --------------------
1           George               George
1           9.5                  9.5
2           Bill                 Bill
2           10.5                 10.5
3           John                 John
3           9                    9
4           Greg                 Greg
4           9                    9</pre>

This query is simply duplicating the data in two columns. Ultimately, we will want the second column to show the person&#8217;s name, and the third column to show the shoe size. For the next step, let&#8217;s show just the name in the second column and the shoe size in the third. We will do this using a case expression:

sql
Select Id,
       Case When Name = 'Name' Then Value End,
       Case When Name = 'ShoeSize' Then Value End
From   #Temp
```

<pre>Id                               
----------- -------------------- --------------------
1           George               NULL
1           NULL                 9.5
2           Bill                 NULL
2           NULL                 10.5
3           John                 NULL
3           NULL                 9
4           Greg                 NULL
4           NULL                 9</pre>

Take a look at the case expression. Notice that there is no ELSE clause. Without an ELSE, the CASE expression will return NULL. This is extremely important for our end result. However, it&#8217;s important to realize that the second column has the person&#8217;s name and NULL, and the third column has shoe size and null. Next we will introduce column aliases.

sql
Select Id,
       Case When Name = 'Name' Then Value End As Name,
       Case When Name = 'ShoeSize' Then Value End As ShoeSize
From   #Temp
```

<pre>Id          Name                 ShoeSize
----------- -------------------- --------------------
1           George               NULL
1           NULL                 9.5
2           Bill                 NULL
2           NULL                 10.5
3           John                 NULL
3           NULL                 9
4           Greg                 NULL
4           NULL                 9</pre>

Nothing fancy here. Just the column names. We still have NULL&#8217;s in our data that we will want to eliminate. Which leads us to our next step, and the most important part, too. When we use aggregates, it&#8217;s important to realize that they ignore NULL&#8217;s in the data. For example, if we have 2 rows with &#8220;George&#8221; in one row and NULL in the other row, Max(Column) will ignore the NULL and return George. We can use that to our advantage here.

sql
Select Id,
       Min(Case When Name = 'Name' Then Value End) As Name,
       Min(Case When Name = 'ShoeSize' Then Value End) As ShoeSize
From   #Temp
Group By Id
```

<pre>Id          Name                 ShoeSize
----------- -------------------- --------------------
1           George               9.5
2           Bill                 10.5
3           John                 9
4           Greg                 9
</pre>

As you can see, we finally got the results we wanted, effectively pivoting the data using code that will comfortably run in SQL2000 (and many other databases, too).

 [1]: /index.php/DataMgmt/DBProgramming/come-one-come-all-to
 [2]: http://twitter.com/grrl_geek
 [3]: /index.php/All/?disp=authdir&author=420