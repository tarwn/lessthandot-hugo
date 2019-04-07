---
title: 'SQL Advent 2012 Day 6: Standardized Naming And Other Conventions'
author: SQLDenis
type: post
date: 2012-12-06T09:36:00+00:00
ID: 1831
excerpt: |
  Every company needs to have standards that developers need to follow in order to make maintenance easier down the road. There are several things that you can standardize on:
  
  The naming of objects
  The layout of code including comments
  The way that e&hellip;
url: /index.php/datamgmt/dbprogramming/standardized-naming-and-other-conventions/
views:
  - 27036
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - conventions
  - ddl
  - designers
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
This is day six of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at standardized naming conventions and other conventions that you should standardize as well. Every company needs to have standards that developers need to follow in order to make maintenance easier down the road. There are several things that you can standardize on, here are just a few:

The naming of objects
  
The layout of code including comments
  
The way that error handling is done

## The naming of objects

I am not a fan of underscores and we tend to name our objects CamelCased
  
Stored procedures are usually prefixed with usp_ or pr but never sp_
  
I also wrote about about this in the using the [ISO-11179 Naming Conventions][2] post
  
Never use Hungarian notation on column names or variables, I have worked with tables that looked like this

sql
CREATE TABLE tblEmployee(
strFirstName varchar(255),
strLastName varchar(255),
intAge	int,
dtmBirthDate datetime
.......
.......
)
```
If you have intellisense in SSMS, having every table start with tbl is making it pretty useless. Also sometimes the data type of a column will change but of course nobody goes back to rename the column to reflect this because it will break code all over the place

Instead of 

sql
-- the salary for the employee
declare @decValue decimal(20,2)
```

It would be better to have something like the following

sql
declare @EmployeeSalary decimal(20,2)
```

Now I don&#8217;t have to scroll all the way to the top to figure out what is actually stored in this variable, EmployeeSalary pretty much describes what it is and I can also pretty much assume that this will be some amount and not a date as well

## The layout of code including comments

I have worked with code that was all in lowercase and all in uppercase. I have no problem with either but if you at least standardize on one or the other it will be a little easier to jump from your code to someone else&#8217;s code
  
You can setup standard templates in SSMS for your organization, you can get to it from the menu bar, View&#8211;> Template Explorer or hit CTRL + ALT + T

Now expand the Stored Procedures folder

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/TemplateBrowserProcs.PNG?mtime=1354732944"><img alt="SSMS Template Browser Stored Procedures Folder Expanded" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/TemplateBrowserProcs.PNG?mtime=1354732944" width="254" height="292" /></a>
</div>

The basic stored procedure template looks like this

sql
-- =============================================
-- Create basic stored procedure template
-- =============================================

-- Drop stored procedure if it already exists
IF EXISTS (
  SELECT * 
    FROM INFORMATION_SCHEMA.ROUTINES 
   WHERE SPECIFIC_SCHEMA = N'<Schema_Name, sysname, Schema_Name>'
     AND SPECIFIC_NAME = N'<Procedure_Name, sysname, Procedure_Name>' 
)
   DROP PROCEDURE <Schema_Name, sysname, Schema_Name>.<Procedure_Name, sysname, Procedure_Name>
GO

CREATE PROCEDURE <Schema_Name, sysname, Schema_Name>.<Procedure_Name, sysname, Procedure_Name>
	<@param1, sysname, @p1> <datatype_for_param1, , int> = <default_value_for_param1, , 0>, 
	<@param2, sysname, @p2> <datatype_for_param2, , int> = <default_value_for_param2, , 0>
AS
	SELECT @p1, @p2
GO

-- =============================================
-- Example to execute the stored procedure
-- =============================================
EXECUTE <Schema_Name, sysname, Schema_Name>.<Procedure_Name, sysname, Procedure_Name> <value_for_param1, , 1>, <value_for_param2, , 2>
GO
```
You can modify this template, give it to every developer and now you all have the same template. What can be done with templates can also be done with snippets, if you do Tools&#8211;>Code Snippets Manager, you can see all the snippets that are available, you can add your own snippets so that all developers will have the same snippets for comment tasks.

Standardize on comments as well
  

  


## The way that error handling is done

I like to have all the errors in one place, this way I know where to look if there are errors. Capture the proc or trigger that threw the error, it if is a multi-step proc then also note the code section in the proc, this will greatly reduce the time it will take you to pinpoint where the problem is. Michelle Ufford has a nice example here: [Error Handling in T-SQL][3] that you can use and implement in your own shop.

There are many more things that you need to standardize on, the thing that bothers me the most is when I see dates in all kind of formats when passed in as strings, use YYYYMMDD, this will make it non ambiguous.

That is all for day six of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][4]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DataDesign/iso-11179-naming-conventions
 [3]: http://sqlfool.com/2008/12/error-handling-in-t-sql/
 [4]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap