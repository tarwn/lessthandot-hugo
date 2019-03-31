---
title: 'SQL Server Auditing: Creating a Database Specification'
author: SQLArcher
type: post
date: 2011-12-28T05:02:00+00:00
excerpt: |
  Continuing onwards with the SQL Server auditing feature, let's start off by creating a simple audit that will capture some database level events.
  
  Previously we looked at how to create a Server Audit Specification and creating a Database Specification&hellip;
url: /index.php/datamgmt/dbadmin/sql-server-auditing-creating-a-1/
views:
  - 7547
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
Continuing onwards with the SQL Server auditing feature, let&#8217;s start off by creating a simple audit that will capture some database level events.

Previously we looked at how to create a Server Audit Specification and creating a Database Specification follows the same steps. The main difference is the location, as well as the objects to capture. In the following example we will look at capturing delete statements on a database.

_It is also important to note, that you cannot audit across databases, you will need to set up a Database Specification and its corresponding Audit per database._

### Creating the Audit

We follow the same steps as previously by creating the Audit file:

Expand your instance >> Security >> Right-Click the audit folder >> Select New Audit

This should bring up a new window similar to this:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/AuditFilejpg.jpg?mtime=1325055918"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/AuditFilejpg.jpg?mtime=1325055918" width="650" height="584" /></a>
</div>

### Creating the Specification

To Create a Database Specification you can find it under:

Database >> Security >> Database Audit Specification

The first configuration I have for the Database Specification, I am capturing on a database level, only deletes, and only when executed by user belonging to the db_datawriter role.

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/DBSpec_1.jpg?mtime=1325062219"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/DBSpec_1.jpg?mtime=1325062219" width="856" height="152" /></a>
</div>

I executed the following query on my account which is sysadmin:

<pre>USE testdb;
GO

DELETE FROM tab1 WHERE id BETWEEN 9990 AND 10000;
GO
DELETE FROM tab2 WHERE id BETWEEN 5000 AND 5100;
GO
DELETE FROM tab3 WHERE id BETWEEN 2000 AND 2002;
GO</pre>

This resulted in no deletes being captured:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/result1.jpg?mtime=1325057639"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/result1.jpg?mtime=1325057639" width="902" height="171" /></a>
</div>

When executed as test\_writer, which belongs to the db\_datawriter role and I execute the following:

<pre>USE testdb;
GO

DELETE FROM tab1 WHERE id BETWEEN 9900 AND 9990;
GO
DELETE FROM tab2 WHERE id BETWEEN 4500 AND 4990;
GO
DELETE FROM tab3 WHERE id BETWEEN 2500 AND 2560;
GO</pre>

I receive the following results:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/result2.jpg?mtime=1325057650"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/result2.jpg?mtime=1325057650" width="1099" height="130" /></a>
</div>

_One thing to note on the output is under &#8220;audit\_file\_offset&#8221;. All three values are 6144, which indicates that the three statements where executed in the same query._

As with the Server Audits, use the following query to retrieve the audit information as it is displayed above:

<pre>SELECT  event_time ,
        action_id ,
        succeeded ,
        session_id ,
        server_principal_name ,
        server_instance_name ,
        database_name ,
        [statement] ,
        audit_file_offset
FROM    fn_get_audit_file('E:SQLAuditingDBAudit*.sqlaudit',
                          DEFAULT, DEFAULT)</pre>

The second example is just to audit a specific table:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/DBSpec_2.jpg?mtime=1325062315"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/DBSpec_2.jpg?mtime=1325062315" width="853" height="154" /></a>
</div>

_Note the areas in the red squares, it changes to audit objects, then a specific table, and lastly to which principle it should be bound and again in this case it will be db_datawriter._

Again, I execute a delete statement:

<pre>USE testdb;
GO

DELETE FROM tab1 WHERE id BETWEEN 1 AND 100;
GO
DELETE FROM tab2 WHERE id BETWEEN 400 AND 6000;
GO
DELETE FROM tab3 WHERE id BETWEEN 7000 AND 7050;
GO</pre>

Inspecting the statement column, we can see that it only captured the delete statement that was executed against tab1:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/result3.jpg?mtime=1325062845"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/DatabaseSpec/result3.jpg?mtime=1325062845" width="1087" height="89" /></a>
</div>

To create the users, database, and populate it with the data I used &#8211; run the following:

<pre>/*Creates the database and the logins*/

USE [master]
GO

IF EXISTS(SELECT NAME FROM master.sys.server_principals WHERE name = 'testuser')
DROP LOGIN testuser;
IF EXISTS(SELECT NAME FROM master.sys.server_principals WHERE name = 'test_writer')
DROP LOGIN test_writer;
IF EXISTS(SELECT NAME FROM master.sys.databases WHERE name = 'testdb')
DROP DATABASE testdb;
GO

CREATE LOGIN [testuser] WITH PASSWORD=N'password', DEFAULT_DATABASE=[master], DEFAULT_LANGUAGE=[us_english],
CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO

ALTER LOGIN [testuser] ENABLE
GO

CREATE LOGIN [test_writer] WITH PASSWORD=N'password', DEFAULT_DATABASE=[master], DEFAULT_LANGUAGE=[us_english],
CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO

ALTER LOGIN [test_writer] ENABLE
GO

CREATE DATABASE [testdb] ;
GO

/* Grants the access for the logins */
USE [testdb] ;
GO

CREATE USER [testuser] FOR LOGIN [testuser] WITH DEFAULT_SCHEMA=[dbo]
GO
CREATE USER [test_writer] FOR LOGIN [test_writer] WITH DEFAULT_SCHEMA=[dbo]
GO
EXEC sp_addrolemember N'db_owner', N'testuser' ;
EXEC sp_addrolemember N'db_datawriter', N'test_writer' ;
GO

/*Creates the test tables */

CREATE TABLE [dbo].[tab1]
    (
      [id] [int] IDENTITY(1, 1)
                 NOT NULL ,
      [comment] [varchar](10) NULL ,
      [tabdate] [date] NULL
    )
ON  [PRIMARY]

GO
CREATE TABLE [dbo].[tab2]
    (
      [id] [int] IDENTITY(1, 1)
                 NOT NULL ,
      [comment] [varchar](10) NULL ,
      [tabdate] [date] NULL
    )
ON  [PRIMARY]

GO
CREATE TABLE [dbo].[tab3]
    (
      [id] [int] IDENTITY(1, 1)
                 NOT NULL ,
      [comment] [varchar](10) NULL ,
      [tabdate] [date] NULL
    )
ON  [PRIMARY]
GO

USE [master]
GO
ALTER DATABASE [testdb] SET  READ_WRITE 
GO


/* Random test data */
USE testdb ;
GO

INSERT  INTO tab1
        ( comment, tabdate )
VALUES  ( 'abcdefg', GETDATE() )

GO 10000

INSERT  INTO tab2
        SELECT  comment ,
                tabdate
        FROM    TAB1 ;
GO

INSERT  INTO tab3
        SELECT  comment ,
                tabdate
        FROM    TAB1 ;
GO</pre>

### Final Thoughts

Just based on auditing for delete statements on database can prove to be very dynamic when using the auditing feature. It doesn&#8217;t even stop here, as this was only a demo, there are a lot more you can audit like stored procedures, functions, views, etc. This is a powerful feature especially for monitoring critical and highly sensitive databases.

To see what else you can audit with the Database Specifications go [here][1] and skip down to Database-Level Audit Action Groups.

 [1]: http://msdn.microsoft.com/en-us/library/cc280663.aspx