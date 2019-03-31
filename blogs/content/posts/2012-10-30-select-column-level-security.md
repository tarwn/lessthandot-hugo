---
title: SELECT 1 – Column Level Security
author: Ted Krueger (onpnt)
type: post
date: 2012-10-30T11:08:00+00:00
excerpt: 'A few weeks ago I wrote about the comparison of SELECT 1 and SELECT * with use in EXISTS T-SQL statements.  The subject has been discussed plenty of times but with any subject, revisiting the topic and reasons we write things the way we do, is still val&hellip;'
url: /index.php/datamgmt/dbprogramming/select-column-level-security/
views:
  - 18116
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
A few weeks ago I wrote about the comparison of [SELECT 1 and SELECT *][1] with use in EXISTS T-SQL statements.  The subject has been discussed plenty of times but with any subject, revisiting the topic and reasons we write things the way we do, is still valuable.  Something did come up offline regarding the concept of how SELECT * will expand the complete column list metadata and security as it pertains to column level grants.  A friend, guy who was an MVP for years and still one in my eyes and all around awesome SQL guru, Laerte Junior ([B][2] | [T][3]) pointed out that the SELECT 1 examples would still fail based on a user only having SELECT on one or more columns but not all the columns in the table.

This indicated that the metadata was indeed being examined.  What I found is, it seems the security model, being the first thing SQL Server will account for, is evaluated prior to the full examination of the metadata on the columns.  The article on the comparison still holds some weight on why or why not to use one method over the other.  However, this was absolutely a great thing to bring up as many implementations of SQL Server databases have taken full advantage of column level security grants.

**Example**

The following example will go over what Laerte Junior pointed out.

<pre>USE master
GO
CREATE LOGIN colUser WITH PASSWORD=N'passmein'
GO
USE AdventureWorks
GO
CREATE TABLE tblColPerms (id int,col1 varchar(10),col2 varchar(10))
GO
CREATE USER colUser FOR LOGIN colUser;
GO
GRANT SELECT ON tblColPerms(col2) TO colUser
GO
--Open a new connection with SSMS using colUser</pre>

In the new query window using the context of coluser

<pre>USE AdventureWorks
GO
BEGIN TRY
SELECT col2 FROM tblColPerms
END TRY
BEGIN CATCH
    PRINT ERROR_MESSAGE()
END CATCH;

BEGIN TRY
SELECT col1 FROM tblColPerms
END TRY
BEGIN CATCH
    PRINT ERROR_MESSAGE()
END CATCH;

BEGIN TRY
SELECT 1 FROM tblColPerms
END TRY
BEGIN CATCH
    PRINT ERROR_MESSAGE()
END CATCH;

BEGIN TRY
SELECT col2 FROM tblColPerms WHERE exists (SELECT 1 FROM tblColPerms)
END TRY
BEGIN CATCH
    PRINT ERROR_MESSAGE()
END CATCH;</pre>

The first select statement will function correctly. The next statements will result in errors regarding the security level the colUser has to examine all the columns in the table based on the user of SELECT 1 or SELECT on a column the colUser does not have access to.

> (0 row(s) affected)
  
> The SELECT permission was denied on the column &#8216;col1&#8217; of the object &#8216;tblColPerms&#8217;, database &#8216;AdventureWorks&#8217;, schema &#8216;dbo&#8217;.
  
> The SELECT permission was denied on the column &#8216;id&#8217; of the object &#8216;tblColPerms&#8217;, database &#8216;AdventureWorks&#8217;, schema &#8216;dbo&#8217;.
  
> The SELECT permission was denied on the column &#8216;id&#8217; of the object &#8216;tblColPerms&#8217;, database &#8216;AdventureWorks&#8217;, schema &#8216;dbo&#8217;.</p>
**Summary**

This was a great tip and catch by Laerte Junior.  It also shows that the code we write, although it may be efficient or meeting best practices, can have an impact on the complete cycle of a transaction as it flows through SQL Server.

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/select-vs-select-1-with
 [2]: http://shellyourexperience.com/
 [3]: http://twitter.com/LaerteSQLDBA