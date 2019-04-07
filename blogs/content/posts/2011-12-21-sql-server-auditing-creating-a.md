---
title: 'SQL Server Auditing: Creating a Server Specification'
author: SQLArcher
type: post
date: 2011-12-21T08:01:00+00:00
ID: 1457
excerpt: |
  Continuing onwards with the SQL Server auditing feature, let's start off by creating a simple audit that will capture some server level events.
  
  As a DBA one of the things to know is when login attempts are made, and which ones is failing as it could&hellip;
url: /index.php/datamgmt/dbadmin/sql-server-auditing-creating-a/
views:
  - 7372
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
Continuing onwards with the SQL Server auditing feature, let&#8217;s start off by creating a simple audit that will capture some server level events.

As a DBA one of the things to know is when login attempts are made, and which ones is failing as it could be someone unauthorized trying to gain access . This can be easily retrieved from the SQL error log, however we want to keep track of these events over a period of time, there for the error log might not suffice.

In other circumstances, the system can be under certain policies and regulations which requires you to keep the data for a set amount of time, and we don&#8217;t want other data that is not as critical to be clutter the data we are actually looking to keep.

### Creating The Audit

To start off with the auditing, we will first set up an audit file. The audit file contains the target destination, file size and max roll over files (only applicable to using a file target) as well as what actions to take when an event triggers.

Limitations on the auditing file is, that you can only have one audit file per specification. This will leave you with either capturing multiple actions or action groups to one file, or multiple specifications to multiple audit files. You can also not mix a database specification with a server specification to one audit file.

To create an audit file using SSMS do the following:

Expand your instance >> Security >> Right-Click the audit folder >> Select New Audit

This should bring up a new window similar to this:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/ServerSpec/Audit.jpg?mtime=1324455850"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/ServerSpec/Audit.jpg?mtime=1324455850" width="651" height="585" /></a>
</div>

##### On Audit Log Failure

Here we can select between the different actions to take based upon what audit action is triggered. We might just want to log all the database option changes, and who made the change, then we will only use CONTINUE or if we don&#8217;t allow changes, then we can fail the operation. In more secure environments when we receive certain login failures, the audit mechanism can shut down SQL and prevent a possible security breach.

##### Audit Destination

We can choose between file, application, and the security log. The different options visible is only applicable to the file destination as where the application and security log destinations will make use of the settings applied within Windows Server.

For the file destination there are some handy features like choosing the different sizes and reserving that disk space upfront. In the case of using the audit log due to regulation, it is advised that you keep an eye on the files to ensure that back them up before they are recycled.

After creating the audit, under the same location &#8211; select your audit >> right-click and select Enable Audit.

### Creating The Specification

So we created our audit, which is now happily running in the background however no audit events are being captured. To capture the events we need to set up either a Server Audit Specification or a Database Audit Specification.

Now we will set up a Server Audit Specification, under the same security folder in SSMS,

Right-click Server Audit Specification >> New Server Audit Specification.

This will bring a window similar to this:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/ServerSpec/Specification.jpg?mtime=1324455868"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/ServerSpec/Specification.jpg?mtime=1324455868" width="844" height="299" /></a>
</div>

Under Audit can select the audit you have just created. If you have multiple audits all of them will show up, however if you choose one with a specification already assigned, then it will throw an error and warn you.

You can assign multiple audit actions, or audit action groups to one specification however for this exercise we will only use database\_change\_group to track database changes.After creating the audit the same steps should be followed to enable the specification.

_Note: It is the default behaviour of Management Studio to create the specifications as well as the audits in an offline state._

So now that both the audit and the specification is in place, I made a few changes to a database and now we can look into the file using the following code:

sql
--returns all the columns for the file
SELECT * FROM fn_get_audit_file ('E:SQLAuditingSQLAudit_DBChanges*.sqlaudit',DEFAULT,DEFAULT)

--Returns specific items to aid in analysing the audit file

SELECT  event_time ,
        action_id ,
        succeeded ,
        session_id ,
        server_principal_name ,
        server_instance_name ,
        database_name ,
        [statement] ,
        audit_file_offset
FROM    fn_get_audit_file('E:SQLAuditingSQLAudit_DBChanges*.sqlaudit',
                          DEFAULT, DEFAULT)
```
_Note: All the files will be appended with a guid, there for it is easier to use the name and then a wildcard which is * to make it easier to load the file and to read the code._

The output of my test looks like this:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/ServerSpec/FailedResults.jpg?mtime=1324455860"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/ServerSpec/FailedResults.jpg?mtime=1324455860" width="1081" height="117" /></a>
</div>

This is a quick specification to monitor for failed logins using the Server Audit Specification and to demonstrate how to do it. There is more information for the different action groups to use with the server audit specification [here][1].

 [1]: http://msdn.microsoft.com/en-us/library/cc280663.aspx