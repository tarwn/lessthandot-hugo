---
title: Running static code analysis on SQL Server database with Visual Studio Team System for database architects
author: SQLDenis
type: post
date: 2010-01-31T23:35:26+00:00
url: /index.php/datamgmt/datadesign/running-static-code-analysis-on-sql-serv/
views:
  - 8525
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - code analysis
  - sql server 2008
  - visual studio 2008

---
To me it seems that Visual Studio Team System 2008 Database Edition is the stepchild of the Visual Studio family. Even in shops that have MSDN Universal/Ultimate subscriptions this version is just not used that much. Maybe it is that long name of this product, I still prefer DataDude. I would like to show you that if you do have licenses for this tool then you should use it because it has some great features. Today we will focus on static code analysis.

Before we start, make sure to grab Microsoft Visual Studio Team System 2008 Database Edition GDR R2
  
and install that on top of your Visual Studio Team System 2008 Database Edition. This install adds the following things to Visual Studio

In addition to providing support for SQL Server 2008 database projects, this release incorporates many previously released Power Tools as well as several new features. The new features include distinct Build and Deploy phases, Static Code Analysis and improved integration with SQL CLR projects.

Database Edition no longer requires a Design Database. Therefore, it is no longer necessary to install an instance of SQL Express or SQL Server prior to using Database Edition.
  
You can download Microsoft Visual Studio Team System 2008 Database Edition GDR R2 here http://www.microsoft.com/downloads/details.aspx?FamilyID=bb3ad767-5f69-4db9-b1c9-8f55759846ed&displaylang=en

Once you have everything installed, start up Visual Studio and create a new database project. You should see something similar to the pic below

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//db1.png" alt="" title="" width="630" height="426" />
</div>

Create a new database connection, you can do that by going to View&#8211;>Server Explorer. Under Data Connections add a new connection to your database that you want to connect to, follow the wizard and create the connection. Now I decided to run static code analysis against the aspnetdb database that is used with ASP.NET. If you want to follow along and use this same database, take a look at this post [Setting up SQL Server with ASP.NET MVC][1] to see how to set it up

Once your database is setup go to the Solution explorer, right click on the project and select Import Database Objects and Settings. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//db2.png" alt="" title="" width="276" height="197" />
</div>

The following dialog will be shown

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//db3.png" alt="" title="" width="841" height="301" />
</div>

Pick your connection and then click start

The output will be similar to this

1/30/2010 11:26:40 AM Import of database schema has started.
  
1/30/2010 11:26:45 AM Adding all files to the project&#8230;
  
1/30/2010 11:26:46 AM Finished adding all files to the project.
  
1/30/2010 11:26:46 AM Done
  
1/30/2010 11:26:46 AM Import of database schema is complete.
  
1/30/2010 11:26:46 AM A summary of the import operation has been saved to the log file C:SVNInterrogateASPInterrogateASPImport Schema LogsInterrogateASP_20100130042639.log.
  
1/30/2010 11:26:46 AM Press Finish to continue&#8230;

Here is what the solution explorer looks like, as you can see the folder hierarchy is very similar to the one in SSMS

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//db4.png" alt="" title="" width="345" height="765" />
</div>

Okay now we are ready to run our static code analysis, click on Data&#8211;>Static Code Analysis&#8211;Run
  
![Run Analysis][2]

Here is the output from that, this creates 216 warnings, I have not pasted the whole output here because it is longer than this whole blog post and most warnings are the same but just for different objects. Here is just a small part and we will look at two of those procedures mentioned in this

<span class="MT_smaller">Running Code Analysis&#8230;<br /> Verifying project state&#8230;<br /> Finished verifying project state.<br /> Loading project references&#8230;<br /> Loading project files&#8230;<br /> Building the project model and resolving object interdependencies&#8230;<br /> Validating the project model&#8230;<br /> ASPNET_APPLICATIONS_CREATEAPPLICATION.PROC.SQL(7,79)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_APPLICATIONS_CREATEAPPLICATION.PROC.SQL(24,15)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_CHECKSCHEMAVERSION.PROC.SQL(9,35)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_MEMBERSHIP_CHANGEPASSWORDQUESTIONANDANSWER.PROC.SQL(11,38)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_MEMBERSHIP_CHANGEPASSWORDQUESTIONANDANSWER.PROC.SQL(11,58)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_MEMBERSHIP_CHANGEPASSWORDQUESTIONANDANSWER.PROC.SQL(12,31)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_MEMBERSHIP_CHANGEPASSWORDQUESTIONANDANSWER.PROC.SQL(14,13)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_USERSINROLES_GETUSERSINROLES.PROC.SQL(9,75)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_USERSINROLES_GETUSERSINROLES.PROC.SQL(17,14)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_USERSINROLES_GETUSERSINROLES.PROC.SQL(23,32)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_ISUSERINROLE.PROC.SQL(10,75)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_USERSINROLES_ISUSERINROLE.PROC.SQL(20,31)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_USERSINROLES_ISUSERINROLE.PROC.SQL(27,31)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(10,64)Warning : SR0015 : Microsoft.Performance : Deterministic function call (LOWER) might cause an unnecessary table scan.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(52,32)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(60,83)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(86,32)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(94,83)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(102,35)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(102,47)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(108,23)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(108,36)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(108,56)Warning : SR0010 : Microsoft.Design : Old-style JOIN syntax is used.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(110,7)Warning : SR0004 : Microsoft.Performance : A column without an index that is used as an IN predicate test expression might degrade performance.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(111,7)Warning : SR0004 : Microsoft.Performance : A column without an index that is used as an IN predicate test expression might degrade performance.<br /> ASPNET_USERSINROLES_REMOVEUSERSFROMROLES.PROC.SQL(118,8)Warning : SR0004 : Microsoft.Performance : A column without an index that is used as an IN predicate test expression might degrade performance.<br /> ASPNET_WEBEVENT_LOGEVENT.PROC.SQL(44,9)Warning : SR0014 : Microsoft.Design : Data loss might occur when casting from Decimal to Decimal.<br /> ASPNET_WEBEVENT_LOGEVENT.PROC.SQL(45,9)Warning : SR0014 : Microsoft.Design : Data loss might occur when casting from Decimal to Decimal.<br /> Code analysis complete &#8212; 0 error(s), 216 warning(s)</span>

Now I was intrigued so I picked the aspnet\_Applications\_CreateApplication stored procedure from that list.

<pre>USE [aspnetdb]
GO
/****** Object:  StoredProcedure [dbo].[aspnet_Applications_CreateApplication]    Script Date: 01/17/2010 15:51:15 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER OFF
GO
 
ALTER PROCEDURE [dbo].[aspnet_Applications_CreateApplication]
    @ApplicationName      NVARCHAR(256),
    @ApplicationId        UNIQUEIDENTIFIER OUTPUT
AS
BEGIN
    SELECT  @ApplicationId = ApplicationId FROM dbo.aspnet_Applications WHERE LOWER(@ApplicationName) = LoweredApplicationName
 
    IF(@ApplicationId IS NULL)
    BEGIN
        DECLARE @TranStarted   BIT
        SET @TranStarted = 0
 
        IF( @@TRANCOUNT = 0 )
        BEGIN
            BEGIN TRANSACTION
            SET @TranStarted = 1
        END
        ELSE
            SET @TranStarted = 0
 
        SELECT  @ApplicationId = ApplicationId
        FROM dbo.aspnet_Applications WITH (UPDLOCK, HOLDLOCK)
        WHERE LOWER(@ApplicationName) = LoweredApplicationName
 
        IF(@ApplicationId IS NULL)
        BEGIN
            SELECT  @ApplicationId = NEWID()
            INSERT  dbo.aspnet_Applications (ApplicationId, ApplicationName, LoweredApplicationName)
            VALUES  (@ApplicationId, @ApplicationName, LOWER(@ApplicationName))
        END
 
 
        IF( @TranStarted = 1 )
        BEGIN
            IF(@@ERROR = 0)
            BEGIN
            SET @TranStarted = 0
            COMMIT TRANSACTION
            END
            ELSE
            BEGIN
                SET @TranStarted = 0
                ROLLBACK TRANSACTION
            END
        END
    END
END</pre>

As you can see it uses the LOWER function in this line

WHERE LOWER(@ApplicationName) = LoweredApplicationName

It actually tells you on which line and position this is actually used (ASPNET\_APPLICATIONS\_CREATEAPPLICATION.PROC.SQL(7,79)Warning) so this means line 7, position 79

Here is another stored procedure, this one uses the LOWER function and old style joins

<pre>USE [aspnetdb]
GO
/****** Object:  StoredProcedure [dbo].[aspnet_UsersInRoles_RemoveUsersFromRoles]    Script Date: 01/17/2010 15:53:59 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER OFF
GO
 
ALTER PROCEDURE [dbo].[aspnet_UsersInRoles_RemoveUsersFromRoles]
    @ApplicationName  NVARCHAR(256),
    @UserNames        NVARCHAR(4000),
    @RoleNames        NVARCHAR(4000)
AS
BEGIN
    DECLARE @AppId UNIQUEIDENTIFIER
    SELECT  @AppId = NULL
    SELECT  @AppId = ApplicationId FROM aspnet_Applications WHERE LOWER(@ApplicationName) = LoweredApplicationName
    IF (@AppId IS NULL)
        RETURN(2)
 
 
    DECLARE @TranStarted   BIT
    SET @TranStarted = 0
 
    IF( @@TRANCOUNT = 0 )
    BEGIN
        BEGIN TRANSACTION
        SET @TranStarted = 1
    END
 
    DECLARE @tbNames  TABLE(Name NVARCHAR(256) NOT NULL PRIMARY KEY)
    DECLARE @tbRoles  TABLE(RoleId UNIQUEIDENTIFIER NOT NULL PRIMARY KEY)
    DECLARE @tbUsers  TABLE(UserId UNIQUEIDENTIFIER NOT NULL PRIMARY KEY)
    DECLARE @Num      INT
    DECLARE @Pos      INT
    DECLARE @NextPos  INT
    DECLARE @Name     NVARCHAR(256)
    DECLARE @CountAll INT
    DECLARE @CountU   INT
    DECLARE @CountR   INT
 
 
    SET @Num = 0
    SET @Pos = 1
    WHILE(@Pos <= LEN(@RoleNames))
    BEGIN
        SELECT @NextPos = CHARINDEX(N',', @RoleNames,  @Pos)
        IF (@NextPos = 0 OR @NextPos IS NULL)
            SELECT @NextPos = LEN(@RoleNames) + 1
        SELECT @Name = RTRIM(LTRIM(SUBSTRING(@RoleNames, @Pos, @NextPos - @Pos)))
        SELECT @Pos = @NextPos+1
 
        INSERT INTO @tbNames VALUES (@Name)
        SET @Num = @Num + 1
    END
 
    INSERT INTO @tbRoles
      SELECT RoleId
      FROM   dbo.aspnet_Roles ar, @tbNames t
      WHERE  LOWER(t.Name) = ar.LoweredRoleName AND ar.ApplicationId = @AppId
    SELECT @CountR = @@ROWCOUNT
 
    IF (@CountR <> @Num)
    BEGIN
        SELECT TOP 1 N'', Name
        FROM   @tbNames
        WHERE  LOWER(Name) NOT IN (SELECT ar.LoweredRoleName FROM dbo.aspnet_Roles ar,  @tbRoles r WHERE r.RoleId = ar.RoleId)
        IF( @TranStarted = 1 )
            ROLLBACK TRANSACTION
        RETURN(2)
    END
 
 
    DELETE FROM @tbNames WHERE 1=1
    SET @Num = 0
    SET @Pos = 1
 
 
    WHILE(@Pos <= LEN(@UserNames))
    BEGIN
        SELECT @NextPos = CHARINDEX(N',', @UserNames,  @Pos)
        IF (@NextPos = 0 OR @NextPos IS NULL)
            SELECT @NextPos = LEN(@UserNames) + 1
        SELECT @Name = RTRIM(LTRIM(SUBSTRING(@UserNames, @Pos, @NextPos - @Pos)))
        SELECT @Pos = @NextPos+1
 
        INSERT INTO @tbNames VALUES (@Name)
        SET @Num = @Num + 1
    END
 
    INSERT INTO @tbUsers
      SELECT UserId
      FROM   dbo.aspnet_Users ar, @tbNames t
      WHERE  LOWER(t.Name) = ar.LoweredUserName AND ar.ApplicationId = @AppId
 
    SELECT @CountU = @@ROWCOUNT
    IF (@CountU <> @Num)
    BEGIN
        SELECT TOP 1 Name, N''
        FROM   @tbNames
        WHERE  LOWER(Name) NOT IN (SELECT au.LoweredUserName FROM dbo.aspnet_Users au,  @tbUsers u WHERE u.UserId = au.UserId)
 
        IF( @TranStarted = 1 )
            ROLLBACK TRANSACTION
        RETURN(1)
    END
 
    SELECT  @CountAll = COUNT(*)
    FROM    dbo.aspnet_UsersInRoles ur, @tbUsers u, @tbRoles r
    WHERE   ur.UserId = u.UserId AND ur.RoleId = r.RoleId
 
    IF (@CountAll <> @CountU * @CountR)
    BEGIN
        SELECT TOP 1 UserName, RoleName
        FROM         @tbUsers tu, @tbRoles tr, dbo.aspnet_Users u, dbo.aspnet_Roles r
        WHERE        u.UserId = tu.UserId AND r.RoleId = tr.RoleId AND
                     tu.UserId NOT IN (SELECT ur.UserId FROM dbo.aspnet_UsersInRoles ur WHERE ur.RoleId = tr.RoleId) AND
                     tr.RoleId NOT IN (SELECT ur.RoleId FROM dbo.aspnet_UsersInRoles ur WHERE ur.UserId = tu.UserId)
        IF( @TranStarted = 1 )
            ROLLBACK TRANSACTION
        RETURN(3)
    END
 
    DELETE FROM dbo.aspnet_UsersInRoles
    WHERE UserId IN (SELECT UserId FROM @tbUsers)
      AND RoleId IN (SELECT RoleId FROM @tbRoles)
    IF( @TranStarted = 1 )
        COMMIT TRANSACTION
    RETURN(0)
END</pre>

So, as you can see it is pretty simple to use Visual Studio Team System 2008 Database Edition to perform static code analysis, the question is of course if you dare to run this against your own database?

I will be back with another post to explore some other things that this tool can do

\*** **Remember, if you have a SQL related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: /index.php/WebDev/ServerProgramming/ASPNET/setting-up-sql-server-with-asp-net-mvc
 [2]: http://imgur.com/oqRCC.png "Run Analysis"
 [3]: http://forum.lessthandot.com/viewforum.php?f=17
 [4]: http://forum.lessthandot.com/viewforum.php?f=22