---
title: 'SQL Advent 2012 Day 20: Use the new features'
author: SQLDenis
type: post
date: 2012-12-20T16:56:00+00:00
ID: 1871
excerpt: |
  This is day twenty of the SQL Advent 2012 series of blog posts. Today we are going to look at why you should use the new features of the product or language
  
  
    
  That is all for day twenty of the SQL Advent 2012 series, come back tomorrow for the ne&hellip;
url: /index.php/webdev/business-intelligence/use-the-new-features/
views:
  - 4555
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence

---
This is day twenty of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at why you should use the new features of the product or language.

Every now and then I get to look at some code that is recently written, looking at it you would think the code is written in 1999. I see insert into temp tables with an identity column in order to return a row number. I also see the old style way of doing a transpose/pivot. There are many more examples I could write down but I am not going to bore you with it here. Why is this bad? I can think of two examples.

  * You are not keeping your skills up to date
  * Even though your code is still valid, it might not be in the next version of the product

Let&#8217;s take a quick look at both of these scenarios

## Even though your code is still valid, it might not be in the next version of the product

Even though the text data type is deprecated, you can still use the text data type, however in the next version of SQL Server it might not be allowed anymore. Now you have a problem if you want to move to the next version. Luckily there is a dynamic management view that you can use to see everything that has been deprecated.

To quickly find out if you are using features that have been deprecated, you can run the following query

sql
select instance_name,cntr_value
from sys.dm_os_performance_counters
where Object_name = 'SQLServer:Deprecated Features'
and cntr_value > 0
```
That might give you back something like the following

<pre>SET ROWCOUNT                                                  	1485
Database compatibility level 80                               	21
DATABASEPROPERTYEX('IsFullTextEnabled')                       	369
DATABASEPROPERTY                                              	22576
INDEX_OPTION                                                  	25
DBCC SHOWCONTIG                                               	4
Table hint without WITH                                       	603
Data types: text ntext or image                               	546
More than two-part column name                                	2</pre>

You can read more about this in the following blog post: [Find Out If You Are Using Deprecated Features In SQL Server 2008][2]. You can now use the output of that query to figure out what not to use in the future.

In java and .NET you can also check if code has been deprecated, here is what it would look like

In java

```java
/**
 * @deprecated  Use this in the future and you will suffer
 */
@Deprecated  
public void doStuff() {  
  // ...  
} 
```

In .NET

```c#
[Obsolete("Use this in the future and you will suffer")]
public void doStuff() {  
  // ...  
}  
```

Now that you know you are still using some of these features in your code, it is time to start abandoning the use of code like that for new development.

## You are not keeping your skills up to date

In the [Stay relevant and marketable][3] post I already explained why it is important to use the latest version. However just using the latest version doesn&#8217;t mean you are also using the latest T-SQL features or the latest Java or .NET libraries. So you might be &#8220;using&#8221; SQL Server 2012 but if all your code in the database was restored from a 2005 backup you are not using any SQL Server 2012 code. Now when you go for an interview and they ask you about some of the new things that they added like the MERGE statement.

Here are some of my posts about the new stuff that has been added to SQL Server since version 2005

01: [Date and time][4]
  
02: [System tables and catalog views][5]
  
03: [Partitioning][6]
  
04: [Schemas][7]
  
05: [Common Table Expressions][8]
  
06: [Windowing functions][9]
  
07: [Crosstab with PIVOT][10]
  
08: [UNPIVOT][11]
  
09: [Dynamic TOP][12]
  
10: [Upsert by using the Merge statement][13]
  
11: [DML statements with the OUTPUT clause][14]
  
12: [Table Value Constructor][15]
  
13: [DDL Triggers][16]
  
14: [EXCEPT and INTERSECT SET Operations][17]
  
15: [Joins][18]
  
16: [CROSS APPLY and OUTER APPLY][19]
  
17: [varchar(max)][20]
  
18: [Table-valued Parameters][21]
  
19: [Filtered Indexes][22]
  
20: [Indexes with Included Columns][23]
  
21: [TRY CATCH][24]
  
22: [Dynamic Management Views][25]
  
23: [OBJECT_DEFINITION][26]
  
24: [Index REBUILD and REORGANIZE][27]

That is all for day twenty of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][28]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DBProgramming/MSSQLServer/find-out-if-you-are-using-deprecated-fea-2008
 [3]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/stay-relevant-and-marketable
 [4]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-1
 [5]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-2
 [6]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-3
 [7]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-4
 [8]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-5
 [9]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-6
 [10]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-7
 [11]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-8
 [12]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-9
 [13]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-10
 [14]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-11
 [15]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-12
 [16]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-13
 [17]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-14
 [18]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-15
 [19]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-16
 [20]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-17
 [21]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-18
 [22]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-19
 [23]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-20
 [24]: /index.php/DataMgmt/DBProgramming/MSSQLServer/try-catch-sql-advent-2011
 [25]: /index.php/DataMgmt/DataDesign/dynamic-management-views
 [26]: /index.php/DataMgmt/DBProgramming/MSSQLServer/object_definition-sql-advent-2011-day
 [27]: /index.php/DataMgmt/DataDesign/index-rebuild-and-reorganize-sql
 [28]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap