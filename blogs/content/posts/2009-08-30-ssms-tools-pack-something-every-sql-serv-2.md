---
title: SSMS Tools PACK, Something Every SQL Server Developer That Uses SSMS Should Have Installed
author: SQLDenis
type: post
date: 2009-08-30T08:32:00+00:00
ID: 546
excerpt: |
  This post was already published yesterday, someone deleted it by mistake so I had to recreate it...sorry for that (and thanks for google cache  :-))
  
  SSMS Tools PACK is an Add-In (Add-On) for Microsoft SQL Server Management Studio and Microsoft SQL Se&hellip;
url: /index.php/datamgmt/dbprogramming/ssms-tools-pack-something-every-sql-serv-2/
views:
  - 30790
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - IBM DB2
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - MySQL
tags:
  - sql server 2005. tools
  - sql server 2008
  - ssms

---
This post was already published yesterday, someone deleted it by mistake so I had to recreate it...sorry for that (and thanks for google cache :-))

SSMS Tools PACK is an Add-In (Add-On) for Microsoft SQL Server Management Studio and Microsoft SQL Server Management Studio Express, this tool is developed by [Mladen Prajdić][1]. 

SSMS Tools PACK contains a few upgrades to the IDE that were missing from Management Studio:</p> 

  * [Window Connection Coloring.][2]{.Titles}
  * [Query Execution History (Soft Source Control) and Current Window History.][3]{.Titles}
  * [Search Table or Database Data.][4]{.Titles}
  * [Uppercase/Lowercase keywords and proper case Database Object Names.][5]{.Titles}
  * [Run one script on multiple databases.][6]{.Titles}
  * [Copy execution plan bitmaps to clipboard.][7]{.Titles}
  * [Search Results in Grid Mode and Execution Plans.][8]{.Titles}
  * [Generate Insert statements for a single table, the whole database or current resultsets in grids.][9]{.Titles}
  * [Text document Regions and Debug sections.][10]{.Titles}
  * [Running custom scripts from Object explorer's Context menu.][11]{.Titles}
  * [CRUD (Create, Read, Update, Delete) stored procedure generation.][12]{.Titles}
  * [New query template.][13]{.Titles}

Some of these features are now available in SSMS 2008 but the tool is still very useful if you are using SSMS 2008. I am going to focus on the CRUD (Create, Read, Update, Delete) stored procedure generation functionality.

SSMS Tools PACK is available for the following SSMS versions

SQL Server Management Studio 2008    

  
SQL Server Management Studio 2008 Express   

  
SQL Server Management Studio 2005   

  
SQL Server Management Studio 2005 Express

Download SSMS Tools PACK here: <http://www.ssmstoolspack.com/Download.aspx>

After it is installed create this table

```sql
CREATE TABLE Test (ID INT PRIMARY KEY IDENTITY,
                    LastName VARCHAR(40) not null,
                    FirstName VARCHAR(40) not null,
                    MiddleInitial CHAR(1)  null,
                    Salutation VARCHAR(10) null,
                    InsertedDate DATETIME not null,
                    LastUpdatedDate DATETIME not null)
```

Now from the menu select SSMS Tools–>CRUD Generator–>Options (see pic below)

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DesktopDev/Denis/SSMS_1.png?mtime=1357606468"><img alt="" src="/wp-content/uploads/blogs/DesktopDev/Denis/SSMS_1.png?mtime=1357606468" width="452" height="280" /></a>
</div>



I like to prefix my procs with pr instead of usp_ (See pic below)



<div class="image_block">
  <a href="/wp-content/uploads/blogs/DesktopDev/Denis/SSMS_2.png?mtime=1357606476"><img alt="" src="/wp-content/uploads/blogs/DesktopDev/Denis/SSMS_2.png?mtime=1357606476" width="650" height="560" /></a>
</div>



You can customize how the procs will be generated, so for example the INSERT proc template looks like this

```sql
IF OBJECT_ID('[|schema|].[|procName|]') IS NOT NULL
BEGIN
    DROP PROC [|schema|].[|procName|]
END
GO
 
/*
Created by: SQLDenis
Version:    1.0
 
 
 
*/
CREATE PROC [|schema|].[|procName|]
|inputParams|
AS
    SET NOCOUNT ON
    SET XACT_ABORT ON  
   
    BEGIN TRAN
   
    INSERT INTO [|schema|].[|tableName|] (|insertColumnList|)
    SELECT |values|
   
    -- Begin Return Select <- do not remove
    SELECT |selectColumnList|
    FROM   [|schema|].[|tableName|]
    WHERE  |whereStatement|
    -- End Return Select <- do not remove
               
    COMMIT
GO
```

As you can see I added a comment block there that has my name and the initial version number. Having all your developers use a tool like this is great because you will have all the same looking procs and don't have to worry that some people use @Error or @ErrorCode or @ErrorID. You just modify the template to have the error checking that you have standardized upon and you are done

So now let's see what SSMS Tools PACK generates

Right click on the table you created, select SSMS Tools and then Create CRUD (See image below)



<div class="image_block">
  <a href="/wp-content/uploads/blogs/DesktopDev/Denis/SSMS_3.png?mtime=1357606486"><img alt="" src="/wp-content/uploads/blogs/DesktopDev/Denis/SSMS_3.png?mtime=1357606486" width="482" height="430" /></a>
</div></p> 

The following code will be generated

```sql
USE [Test];
GO
 
IF OBJECT_ID('[dbo].[prTestSelect]') IS NOT NULL
BEGIN
    DROP PROC [dbo].[prTestSelect]
END
GO
 
/*
Created by: SQLDenis
Version:    1.0
 
 
 
*/
CREATE PROC [dbo].[prTestSelect]
    @ID INT
AS
    SET NOCOUNT ON
    SET XACT_ABORT ON  
 
    BEGIN TRAN
 
    SELECT [ID], [FirstName], [InsertedDate], [LastName], [LastUpdatedDate], [MiddleInitial], [Salutation]
    FROM   [dbo].[Test]
    WHERE  ([ID] = @ID OR @ID IS NULL)
 
    COMMIT
GO
IF OBJECT_ID('[dbo].[prTestInsert]') IS NOT NULL
BEGIN
    DROP PROC [dbo].[prTestInsert]
END
GO
 
/*
Created by: SQLDenis
Version:    1.0
 
 
 
*/
CREATE PROC [dbo].[prTestInsert]
    @FirstName VARCHAR(40),
    @InsertedDate DATETIME,
    @LastName VARCHAR(40),
    @LastUpdatedDate DATETIME,
    @MiddleInitial CHAR(1),
    @Salutation VARCHAR(10)
AS
    SET NOCOUNT ON
    SET XACT_ABORT ON  
   
    BEGIN TRAN
   
    INSERT INTO [dbo].[Test] ([FirstName], [InsertedDate], [LastName], [LastUpdatedDate], [MiddleInitial], [Salutation])
    SELECT @FirstName, @InsertedDate, @LastName, @LastUpdatedDate, @MiddleInitial, @Salutation
   
    -- Begin Return Select <- do not remove
    SELECT [ID], [FirstName], [InsertedDate], [LastName], [LastUpdatedDate], [MiddleInitial], [Salutation]
    FROM   [dbo].[Test]
    WHERE  [ID] = SCOPE_IDENTITY()
    -- End Return Select <- do not remove
               
    COMMIT
GO
IF OBJECT_ID('[dbo].[prTestUpdate]') IS NOT NULL
BEGIN
    DROP PROC [dbo].[prTestUpdate]
END
GO
 
/*
Created by: SQLDenis
Version:    1.0
 
 
 
*/
CREATE PROC [dbo].[prTestUpdate]
    @ID INT,
    @FirstName VARCHAR(40),
    @InsertedDate DATETIME,
    @LastName VARCHAR(40),
    @LastUpdatedDate DATETIME,
    @MiddleInitial CHAR(1),
    @Salutation VARCHAR(10)
AS
    SET NOCOUNT ON
    SET XACT_ABORT ON  
   
    BEGIN TRAN
 
    UPDATE [dbo].[Test]
    SET    [FirstName] = @FirstName, [InsertedDate] = @InsertedDate, [LastName] = @LastName, [LastUpdatedDate] = @LastUpdatedDate, [MiddleInitial] = @MiddleInitial, [Salutation] = @Salutation
    WHERE  [ID] = @ID
   
    -- Begin Return Select <- do not remove
    SELECT [ID], [FirstName], [InsertedDate], [LastName], [LastUpdatedDate], [MiddleInitial], [Salutation]
    FROM   [dbo].[Test]
    WHERE  [ID] = @ID   
    -- End Return Select <- do not remove
 
    COMMIT TRAN
GO
IF OBJECT_ID('[dbo].[prTestDelete]') IS NOT NULL
BEGIN
    DROP PROC [dbo].[prTestDelete]
END
GO
 
/*
Created by: SQLDenis
Version:    1.0
 
 
 
*/
CREATE PROC [dbo].[prTestDelete]
    @ID INT
AS
    SET NOCOUNT ON
    SET XACT_ABORT ON  
   
    BEGIN TRAN
 
    DELETE
    FROM   [dbo].[Test]
    WHERE  [ID] = @ID
 
    COMMIT
GO
 
----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------

```
As you can see that is a huge time saver, you can of course customize it so that it conforms to your style guide.

Don't forget to thank Mladen Prajdić on twitter: <http://twitter.com/MladenPrajdic> or to donate if this tool is useful to you



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][14] forum or our [Microsoft SQL Server Admin][15] forum**<ins></ins>

 [1]: http://weblogs.sqlteam.com/mladenp/
 [2]: http://www.ssmstoolspack.com/Features.aspx#WCC
 [3]: http://www.ssmstoolspack.com/Features.aspx#SSC
 [4]: http://www.ssmstoolspack.com/Features.aspx#SDD
 [5]: http://www.ssmstoolspack.com/Features.aspx#FKW
 [6]: http://www.ssmstoolspack.com/Features.aspx#RMS
 [7]: http://www.ssmstoolspack.com/Features.aspx#CEP
 [8]: http://www.ssmstoolspack.com/Features.aspx#SRE
 [9]: http://www.ssmstoolspack.com/Features.aspx#GIS
 [10]: http://www.ssmstoolspack.com/Features.aspx#RDS
 [11]: http://www.ssmstoolspack.com/Features.aspx#RCS
 [12]: http://www.ssmstoolspack.com/Features.aspx#CRUD
 [13]: http://www.ssmstoolspack.com/Features.aspx#NQT
 [14]: http://forum.ltd.local/viewforum.php?f=17
 [15]: http://forum.ltd.local/viewforum.php?f=22