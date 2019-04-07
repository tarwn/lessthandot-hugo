---
title: 'Fixing orphaned database users in 2005 to 2012 – T-SQL Tuesday #025'
author: Ted Krueger (onpnt)
type: post
date: 2011-12-13T11:40:00+00:00
excerpt: 'This will be my contribution to the T-SQL Tuesday Challenge that was originally created by Adam Machanic.  This month, my good friend Allen White (Blog | Twitter) is hosting the challenge.  The topic he has chosen is, “What T-SQL tricks do you use today&hellip;'
url: /index.php/datamgmt/dbprogramming/fixing-orphaned-database-users/
views:
  - 64275
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="http://sqlblog.com/blogs/allen_white/archive/2011/12/05/t-sql-tuesday-025-invitation-to-share-your-tricks.aspx"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-19.png?mtime=1323611029" width="154" height="154" align="left" /></a>
</div>

This will be my contribution to the T-SQL Tuesday Challenge that was originally created by Adam Machanic. This month, my good friend Allen White ([Blog][1] | [Twitter][2]) is hosting the challenge. The topic he has chosen is, “What T-SQL tricks do you use today to make your job easier?” I enjoy this topic and understand why he chose it. As our experience grows, we compose dozens if not hundreds of methods in order to get our job done quickly or simply. We’ve all been faced with challenges and found the trick to resolving them given our own unique installations of SQL Server. This is a great way to get a compilation of everyone’s tricks and see how we may share them with the community. 

The orphaned user script can be used to resolve database user disconnections from SQL Server logins after a restore is performed.  There are other applications for this script but the restore problem with user to logins is the one that will be focused on today.

**The problem defined**

A common misconception is that database users and logins are the same objects.  With SQL Server, these are two unique security objects and two completely different levels of security.  A database user can exist without a valid SQL login, and vice versa.  Although these two objects can exist without each other, a database user is usually only effective if a SQL Login is tied to it.  This is the path to connecting to the SQL Server instance itself and the database user is later evaluated internally to the database permissions the user has that is tied to the login.

 

In SQL Server 2012 and the AdventureWorks database, right click the SecurityàUsers nodes and click New User…  Drop the list down and notice the “SQL user without login”.  If this choice is utilized, there will be an orphaned database login, since there is no valid SQL login tied to it.

 

A common problem with this orphaned situation is when a database is restored.  If a database is restored using a default strategy and no steps other than the restore command are taken, all the database users that were created in that restored database will also be restored.  Since these database users did not have a login or the connection to the SQL login has been severed, there are steps that need to be taken to reattach these objects.

 

**Remapping User to Login**

 

To remap a database user to a SQL login, the system procedure SP\_CHANGE\_USERS_LOGIN is available.  This procedure is on the list to be removed from future SQL Server releases and has been from SQL Server 2012 as of RC0.  Instead, the ALTER USER command must be used.  Since the procedure is still used in all the current versions of SQL Server, it will be shown as well as the replacement method of using ALTER USER.  

To call SP\_CHANGE\_USERS_LOGIN, use the following syntax.

 

sp\_change\_users_login [ @Action = ] &#8216;action&#8217;

[ , [ @UserNamePattern = ] &#8216;user&#8217; ]

[ , [ @LoginName = ] &#8216;login&#8217; ]

[ , [ @Password = ] &#8216;password&#8217; ]

[;]

 

The procedure is quite simple but to be effective on a database that was restored with hundreds of logins that may or may not even exist on the SQL Server instance the database was restored to, a script is required.  If a script was not created, the task could potentially take hours to create and remap all the database users.

 

For SQL Server 2012, the ALTER USER is used by simply using dynamic T-SQL to generate the command and set the user name and password. The one feature that previous versions of this script lacked was the differentiation between SQL user logins and Windows logins.  With Windows logins, the password is not set.  When using the ALTER USER method, if a SQL Server login is not found, the CREATE LOGIN statement is called to create a login.  There should be a check for the type of database user first though.  Then the decision can be made on how to build the CREATE LOGIN statement.  This enhancement is added to the SQL Server 2012 version.

 

**The script**

 ****

The below script has a work flow of first identifying the orphaned database users by utilizing the same sp\_change\_users\_login with the ‘report’ action called.  This returns the database users of the database it is executed in and then allows the script to check the sys.server\_principals for a valid login.  If a login is not found, one is created with the same name as the database user.  The password is set as a default password.  This password should be immediately dealt with as a change method by the user or some other compliant method to prevent security problems.  Once the login is created, the sp\_change\_users_login is used to remap the database user to the new SQL login or to an existing login that was found.

 

**SQL Server 2005, 2008, 2008 R2 Versions**

<pre>SET NOCOUNT ON
USE AdventureWorks
GO
DECLARE @loop INT
DECLARE @USER sysname
 
IF OBJECT_ID('tempdb..#Orphaned') IS NOT NULL 
 BEGIN
  DROP TABLE #orphaned
 END
 
CREATE TABLE #Orphaned (UserName sysname, UserSID VARBINARY(85),IDENT INT IDENTITY(1,1))
 
INSERT INTO #Orphaned
EXEC SP_CHANGE_USERS_LOGIN 'report';
 
IF(SELECT COUNT(*) FROM #Orphaned) &gt; 0
BEGIN
 SET @loop = 1
 WHILE @loop <= (SELECT MAX(IDENT) FROM #Orphaned)
  BEGIN
    SET @USER = (SELECT UserName FROM #Orphaned WHERE IDENT = @loop)
    IF(SELECT COUNT(*) FROM sys.server_principals WHERE [Name] = @USER) <= 0
     BEGIN
        EXEC SP_ADDLOGIN @USER,'password'
     END
     
    EXEC SP_CHANGE_USERS_LOGIN 'update_one',@USER,@USER
    PRINT @USER + ' link to DB user reset';
    SET @loop = @loop + 1
  END
END
SET NOCOUNT OFF</pre>

**SQL Server 2005, 2008, 2008 R2 and 2012 Version with Windows Login check**

<pre>SET NOCOUNT ON
USE AdventureWorks
GO
DECLARE @loop INT
DECLARE @USER sysname
DECLARE @sqlcmd NVARCHAR(500) = ''

IF OBJECT_ID('tempdb..#Orphaned') IS NOT NULL 
 BEGIN
  DROP TABLE #orphaned
 END
 
CREATE TABLE #Orphaned (UserName sysname,IDENT INT IDENTITY(1,1))
 
INSERT INTO #Orphaned (UserName)
SELECT [name] FROM sys.database_principals WHERE [type] IN ('U','S') AND is_fixed_role = 0 AND [Name] NOT IN ('dbo','guest','sys','INFORMATION_SCHEMA')

IF(SELECT COUNT(*) FROM #Orphaned) &gt; 0
BEGIN
 SET @loop = 1
 WHILE @loop <= (SELECT MAX(IDENT) FROM #Orphaned)
  BEGIN
    SET @USER = (SELECT UserName FROM #Orphaned WHERE IDENT = @loop)
    IF(SELECT COUNT(*) FROM sys.server_principals WHERE [Name] = @USER) <= 0
     BEGIN
		IF EXISTS(SELECT 1 FROM sys.database_principals WHERE [Name] = @USER AND type_desc = 'WINDOWS_USER')
		 BEGIN
			SET @sqlcmd = 'CREATE LOGIN [' + @USER + '] FROM WINDOWS'
			Exec(@sqlcmd)
			PRINT @sqlcmd
		 END
		IF EXISTS(SELECT 1 FROM sys.database_principals WHERE [Name] = @USER AND type_desc = 'SQL_USER')
		 BEGIN
			SET @sqlcmd = 'CREATE LOGIN [' + @USER + '] WITH PASSWORD = N''password'''
			Exec(@sqlcmd)
			PRINT @sqlcmd
		 END
     END
     
	SET @sqlcmd = 'ALTER USER [' + @USER + '] WITH LOGIN = [' + @USER + ']'
	Exec(@sqlcmd)
    PRINT @USER + ' link to DB user reset';
    SET @loop = @loop + 1
  END
END
SET NOCOUNT OFF</pre>

 [1]: http://sqlblog.com/blogs/allen_white/default.aspx
 [2]: http://twitter.com/SQLRunr