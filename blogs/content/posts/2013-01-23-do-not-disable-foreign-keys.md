---
title: Do not disable foreign keys
author: Ted Krueger (onpnt)
type: post
date: 2013-01-23T11:28:00+00:00
excerpt: 'Foreign keys and primary keys play a crucial part in all relational databases – referential integrity.  Referential integrity is essentially the glue that holds together one or more columns between two or more tables.  This glue dictates if a value is f&hellip;'
url: /index.php/datamgmt/dbadmin/do-not-disable-foreign-keys/
views:
  - 20043
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Foreign keys and primary keys play a crucial part in all relational databases – referential integrity.  [Referential integrity][1] is essentially the glue that holds together one or more columns between two or more tables.  This _glue_ dictates if a value is found in one table, it can then exist in another; primary to foreign relationships.

With referential integrity come more complex situations for manipulating data.  This is seen primarily with deletions, although it’s just as prevalent in updates and insertions.  The importance of referential integrity comes in preventing corruption of the integrity itself.  If referential integrity is compromised, queries may fail, return false information or, in a critical stage, prevent data access all together.

Focusing on delete events, we can take a look at a common but extremely poor practice when a database is designed properly and referential integrity has been implemented and a delete action is attempted but fails.

Let’s say a SQL Developer has been tasked with removing outdated records from a database.  This has been found to be safe, provided an archiving strategy is put in place and the items are now archived out to a secondary source.  The archiving strategy, however, did not provide a method to remove the original items.  Given this, the developer has to remove the items manually at a time not within normal operating hours.  The database was designed by the team’s DBA and has implemented a relationship between the table the items need to be removed from and another table for customer ordering details.

<pre>CREATE TABLE item_table (itemnumber int PRIMARY KEY IDENTITY(1,1), itemdesc varchar(10), itemstatus tinyint)
GO
CREATE TABLE cust_item_ordering (custorder_id BIGINT, itemnumber INT, itemqty INT)
GO
ALTER TABLE cust_item_ordering 
ADD CONSTRAINT fk_itemnumber 
FOREIGN KEY (itemnumber) 
REFERENCES item_table(itemnumber)
GO</pre>

 

The customer ordering details table holds data that is automatically inserted and is then replicated to another source for reporting.  The developer does not really care too much about the customer ordering details because that data is for reporting and if it doesn’t go over, it simply will not replicate.  However, the developer runs a DELETE FROM item_table WHERE itemnumber in (item1, item2) and receives the following error.

<span class="MT_red">Msg 547, Level 16, State 0, Line 1<br /> The DELETE statement conflicted with the REFERENCE constraint &#8220;fk_itemnumber&#8221;. The conflict occurred in database &#8220;QTuner_Design&#8221;, table &#8220;dbo.cust_item_ordering&#8221;, column &#8216;itemnumber&#8217;.<br /> </span>

The statement has been terminated.

 

The developer proceeds to search and finds a solution to get beyond the error.  The developer sends the solution to the DBA as follows.

Please execute the following on server A in database B.  I’ll let you know when to run the next step when I finish getting something done.

<pre>EXEC sp_msforeachtable "ALTER TABLE ? NOCHECK CONSTRAINT all"</pre>

We’ll stop here and discuss this situation.

**The Situation**

This example is being written exactly how it has played out thousands of times in real-life data teams.  It has happened to me hundreds of times and the request has always been denied.  Although the request has been denied, I also replied to it with a solution that will maintain the integrity of the database.

Before going into the solution and reasons not to disable the foreign key, I chose this example because it has a common situation in which the primary key is a critical table but the table where the foreign key resides isn’t.  The foreign key table has been setup to primarily act as a reporting solution.  Even with this situation, the foreign key constraint should not be disabled.  If the foreign key constraint is disabled in order to remove the primary key rows, we are breaking the referential integrity of the foreign key table.  This can lead to report failures, queries that may need to look back at the primary keys for critical data as it moves through an analytical reporting solution or predictive analytical solutions, from either returning no data or inaccurate data.

The best practice in this situation is to never break the referential integrity of the database.  To do so is a form of laziness in maintaining the data as it has been defined by the business needs and overall design implemented to store the data accurately.

**One Solution**

The problem still remains: the items in the item\_table need to be removed.  Initially, we are concerned with the foreign key in the error, fk\_itemnumber.  However, this could be the initial foreign key violation that we run into. If the statement the developer sent out, which disables all foreign keys in the database, is used, other more critical tables may be directly affected.  This becomes a severe issue when the foreign keys are enabled again as the checks will not be done.  Given this common problem, approach this situation with a solution that is more stable and follows best practices.

1)      Find all FK columns

2)      Review those tables and their purpose

3)      If it is found feasible to remove all the related data, first remove the FK data, then the PK

**Find all FK columns**

To find all the FK columns that relate to a specific PK, we can look at the catalog view [sys.foreign_keys][2] and [sys.foreign\_key\_columns][3].  Using the two foreign key catalog views with [sys.objects][4] and [sys.columns,][5] we can obtain all the needed information to further review where to look and what to remove prior to the primary key rows.

<pre>SELECT
 obj_fk.name [Foreign Key Table Name],
 fk_name.name [Foreign Key Column Name],
 fk.name [Foreign Key Constraint Name],
 obj_pk.name [Primary Key Table Name],
 pk_name.name [Primary Key Column Name]
FROM sys.objects obj_fk
 INNER JOIN sys.foreign_keys fk ON obj_fk.object_id = fk.parent_object_id
 INNER JOIN sys.foreign_key_columns cols_fk ON fk.object_id = cols_fk.constraint_object_id
 INNER JOIN sys.columns fk_name ON cols_fk.parent_object_id = fk_name.object_id AND cols_fk.parent_column_id = fk_name.column_id 
 INNER JOIN sys.columns pk_name ON cols_fk.referenced_object_id = pk_name.object_id AND cols_fk.referenced_column_id = pk_name.column_id
 INNER JOIN sys.objects obj_pk ON fk.referenced_object_id = obj_pk.object_id
 WHERE obj_pk.name = 'item_table'</pre>

 

In the results, there is another table that has been identified with a foreign key that is tied back to the primary key of itemnumber.  This table is much more critical and holds the relationships back to the bill of materials (BOM) tables.  This data should be removed prior to the primary key being removed in order to maintain referential integrity.

**Remove the Data** 

To remove the data, first identify if the data should be removed.  If the data is passed for removal, remove the foreign key rows and then, last, remove the primary key data.

<pre>BEGIN TRY
	DELETE FROM cust_item_ordering WHERE itemnumber = 99
	DELETE FROM itemdetail WHERE itemnumber = 99
	DELETE FROM item_table WHERE itemnumber = 99
END TRY
BEGIN CATCH
	SELECT ERROR_NUMBER() AS ErrorNumber;
END CATCH</pre>

 

(1 row(s) affected)

(1 row(s) affected)

(1 row(s) affected)

 

The delete statements execute successfully. This is only due to manually validating the FK data, removing it prior to removing the PK data.  Since these steps have been performed, the validity of the data has been maintained – referential integrity.

**Summary**

Referential integrity truly defines and forces the data moving in and out of a database to maintain accuracy and quality.  While forcing constraints and other mechanisms put in place to be bypassed may sound like a stable solution, best practices are to always follow the constraints and what they are defining in how to maintain the data.

To protect against situations like this, always run [DBCC CHECKCONSTRAINTS][6] periodically to ensure the constraints have been maintained properly.

 [1]: http://en.wikipedia.org/wiki/Referential_integrity
 [2]: http://technet.microsoft.com/en-us/library/ms189807.aspx
 [3]: http://msdn.microsoft.com/en-us/library/ms186306.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms190324.aspx
 [5]: http://msdn.microsoft.com/en-us/library/ms176106.aspx
 [6]: http://msdn.microsoft.com/en-us/library/ms189496.aspx