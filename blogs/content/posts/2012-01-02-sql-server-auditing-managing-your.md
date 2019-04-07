---
title: 'SQL Server Auditing: Managing Your Audits'
author: SQLArcher
type: post
date: 2012-01-02T18:40:00+00:00
excerpt: |
  So far in the SQL Server Audit series we've looked at the different components that make up the auditing feature as well as how to create the audits with specifications specific to databases as well as server level. 
  
  Here is the links to the posts co&hellip;
url: /index.php/datamgmt/dbadmin/sql-server-auditing-managing-your/
views:
  - 11969
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
So far in the SQL Server Audit series we&#8217;ve looked at the different components that make up the auditing feature as well as how to create the audits with specifications specific to databases as well as server level. 

Here is the links to the posts covering the topics:

[SQL Server Auditing: Introduction][1]
  
[SQL Server Auditing: Creating a Server Specification][2]
  
[SQL Server Auditing: Creating a Database Specification][3]

Now that we have set up our audits covering failed logins on instance level and deletes on database level, let&#8217;s take a look at how we can see what audits are on a server as well as some views to make your life easier with reading the audit files.

### DMV&#8217;s and Views

First off, let&#8217;s see what views are available at our disposal:

<pre>USE master;
GO
SELECT name,[object_id] FROM [sys].[all_objects]
WHERE TYPE = 'v' AND NAME LIKE '%audit%';
GO</pre>

This returns the following for us:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/Management/Results.jpg?mtime=1325273512"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/Management/Results.jpg?mtime=1325273512" width="307" height="192" /></a>
</div>

#### DMV&#8217;s

So what does the audit DMV&#8217;s do? Let&#8217;s take a look:

_sys.dm\_audit\_actions_

This DMV contains friendlier names, as well as all the actions as well as which action groups they belong to across database and server level specifications.

_sys.dm\_audit\_class\_type\_map_

This contains all the audit classes and action groups available.

_sys.dm\_server\_audit_status_

This takes a specific look at the audits defined on the server and returns info such as what audits are enabled, statuses for the audits, event times, etc.

_Sys.dm\_audit\_class\_type\_map and sys.dm\_audit\_actions are especially useful to join with the other views to get a more easily readable report on the audits, or to join it with the fn\_get\_audit_file function when reading audit files._

#### Server Views

The server views are split up into two &#8220;sections&#8221;, one for the audits, and the second for the server specification details.

Audits:
  
_sys.server_audits_
  
_sys.server\_file\_audits_

These views contain details about the server level audits defined on the server, This returns information such as audit names, file names and paths, audit id&#8217;s, statuses, etc.

Specification:
  
sys.server\_audit\_specification_details
  
sys.server\_audit\_specifications

These views are very helpful when you have multiple audits on a server, and matching the various defined audit actions as well as their groups to audits.

#### Database Views

The database views available are almost identical to the server level ones, except the fact that it&#8217;s specific to databases, and that there are no &#8220;audit&#8221; views; only for database specifications. Another thing to note is, that these views won&#8217;t return information across databases on an instance, this needs to be executed per database.

_sys.database\_audit\_specification_details_
  
_sys.database\_audit\_specifications_

The views return the specification names, enabled audit actions, audit results, etc.

Now that we&#8217;ve got the views covered, a question still lingers&#8230; &#8220;How do we manage audits across multiple servers?&#8221; Well the answer is quite simple, we make use of another new feature introduced in SQL Server 2008 which is Policy Based Management, otherwise known as PBM.

So PBM is a subject on it&#8217;s own, so I will not go in depth into this topic.

A key resource required for monitoring your environment is the [Enterprise Policy Framework][4].

Below is a test policy I created for the post and will monitor the audits for the following:

1. Maximum File Size
  
2. On Failure Action
  
3. Reserve Disk Space
  
4. Maximum Rollover Files
  
5. Maximum File Size Unit ( MB, GB or TB )

<pre>-- Creates the policy condition

Declare @condition_id int
EXEC msdb.dbo.sp_syspolicy_add_condition @name=N'Audit_Policy_Con', @description=N'', @facet=N'Audit', @expression=N'<Operator&gt;
  <TypeClass&gt;Bool</TypeClass&gt;
  <OpType&gt;AND</OpType&gt;
  <Count&gt;2</Count&gt;
  <Operator&gt;
    <TypeClass&gt;Bool</TypeClass&gt;
    <OpType&gt;AND</OpType&gt;
    <Count&gt;2</Count&gt;
    <Operator&gt;
      <TypeClass&gt;Bool</TypeClass&gt;
      <OpType&gt;AND</OpType&gt;
      <Count&gt;2</Count&gt;
      <Operator&gt;
        <TypeClass&gt;Bool</TypeClass&gt;
        <OpType&gt;AND</OpType&gt;
        <Count&gt;2</Count&gt;
        <Operator&gt;
          <TypeClass&gt;Bool</TypeClass&gt;
          <OpType&gt;EQ</OpType&gt;
          <Count&gt;2</Count&gt;
          <Attribute&gt;
            <TypeClass&gt;Numeric</TypeClass&gt;
            <Name&gt;MaximumFileSize</Name&gt;
          </Attribute&gt;
          <Constant&gt;
            <TypeClass&gt;Numeric</TypeClass&gt;
            <ObjType&gt;System.Double</ObjType&gt;
            <Value&gt;100</Value&gt;
          </Constant&gt;
        </Operator&gt;
        <Operator&gt;
          <TypeClass&gt;Bool</TypeClass&gt;
          <OpType&gt;EQ</OpType&gt;
          <Count&gt;2</Count&gt;
          <Attribute&gt;
            <TypeClass&gt;Numeric</TypeClass&gt;
            <Name&gt;OnFailure</Name&gt;
          </Attribute&gt;
          <Function&gt;
            <TypeClass&gt;Numeric</TypeClass&gt;
            <FunctionType&gt;Enum</FunctionType&gt;
            <ReturnType&gt;Numeric</ReturnType&gt;
            <Count&gt;2</Count&gt;
            <Constant&gt;
              <TypeClass&gt;String</TypeClass&gt;
              <ObjType&gt;System.String</ObjType&gt;
              <Value&gt;Microsoft.SqlServer.Management.Smo.OnFailureAction</Value&gt;
            </Constant&gt;
            <Constant&gt;
              <TypeClass&gt;String</TypeClass&gt;
              <ObjType&gt;System.String</ObjType&gt;
              <Value&gt;Continue</Value&gt;
            </Constant&gt;
          </Function&gt;
        </Operator&gt;
      </Operator&gt;
      <Operator&gt;
        <TypeClass&gt;Bool</TypeClass&gt;
        <OpType&gt;EQ</OpType&gt;
        <Count&gt;2</Count&gt;
        <Attribute&gt;
          <TypeClass&gt;Bool</TypeClass&gt;
          <Name&gt;ReserveDiskSpace</Name&gt;
        </Attribute&gt;
        <Function&gt;
          <TypeClass&gt;Bool</TypeClass&gt;
          <FunctionType&gt;False</FunctionType&gt;
          <ReturnType&gt;Bool</ReturnType&gt;
          <Count&gt;0</Count&gt;
        </Function&gt;
      </Operator&gt;
    </Operator&gt;
    <Operator&gt;
      <TypeClass&gt;Bool</TypeClass&gt;
      <OpType&gt;EQ</OpType&gt;
      <Count&gt;2</Count&gt;
      <Attribute&gt;
        <TypeClass&gt;Numeric</TypeClass&gt;
        <Name&gt;MaximumRolloverFiles</Name&gt;
      </Attribute&gt;
      <Constant&gt;
        <TypeClass&gt;Numeric</TypeClass&gt;
        <ObjType&gt;System.Double</ObjType&gt;
        <Value&gt;100</Value&gt;
      </Constant&gt;
    </Operator&gt;
  </Operator&gt;
  <Operator&gt;
    <TypeClass&gt;Bool</TypeClass&gt;
    <OpType&gt;EQ</OpType&gt;
    <Count&gt;2</Count&gt;
    <Attribute&gt;
      <TypeClass&gt;Numeric</TypeClass&gt;
      <Name&gt;MaximumFileSizeUnit</Name&gt;
    </Attribute&gt;
    <Function&gt;
      <TypeClass&gt;Numeric</TypeClass&gt;
      <FunctionType&gt;Enum</FunctionType&gt;
      <ReturnType&gt;Numeric</ReturnType&gt;
      <Count&gt;2</Count&gt;
      <Constant&gt;
        <TypeClass&gt;String</TypeClass&gt;
        <ObjType&gt;System.String</ObjType&gt;
        <Value&gt;Microsoft.SqlServer.Management.Smo.AuditFileSizeUnit</Value&gt;
      </Constant&gt;
      <Constant&gt;
        <TypeClass&gt;String</TypeClass&gt;
        <ObjType&gt;System.String</ObjType&gt;
        <Value&gt;Mb</Value&gt;
      </Constant&gt;
    </Function&gt;
  </Operator&gt;
</Operator&gt;', @is_name_condition=0, @obj_name=N'', @condition_id=@condition_id OUTPUT
Select @condition_id

GO

-- Creates the Policy

Declare @object_set_id int
EXEC msdb.dbo.sp_syspolicy_add_object_set @object_set_name=N'Audit_Policy_ObjectSet', @facet=N'Audit', @object_set_id=@object_set_id OUTPUT
Select @object_set_id

Declare @target_set_id int
EXEC msdb.dbo.sp_syspolicy_add_target_set @object_set_name=N'Audit_Policy_ObjectSet', @type_skeleton=N'Server/Audit', @type=N'AUDIT', @enabled=True, @target_set_id=@target_set_id OUTPUT
Select @target_set_id

EXEC msdb.dbo.sp_syspolicy_add_target_set_level @target_set_id=@target_set_id, @type_skeleton=N'Server/Audit', @level_name=N'Audit', @condition_name=N'', @target_set_level_id=0
GO

Declare @policy_id int
EXEC msdb.dbo.sp_syspolicy_add_policy @name=N'Audit_Policy', @condition_name=N'Audit_Policy_Con', @policy_category=N'', @description=N'', @help_text=N'', @help_link=N'', @schedule_uid=N'00000000-0000-0000-0000-000000000000', @execution_mode=0, @is_enabled=False, @policy_id=@policy_id OUTPUT, @root_condition_name=N'', @object_set=N'Audit_Policy_ObjectSet'
Select @policy_id
GO</pre>

This is just a basic policy using the &#8220;audit&#8221; facet. There are additional facets for Database Audit Specifications as well as Server Audit Specifications.

Additionally if you decide to use the Enterprise Policy Framework, it should be noted that you need an additional condition to be set up on the policy that will only evaluate the policy against SQL Server 2008+.

### Last Thoughts

These are the views we can use to discover what audits are running across an instance or databases, which actions we are auditing for and a brief policy we can use when we have to manage the audit files specifically to ensure we have a set standard.

 [1]: /index.php/All/?p=1557
 [2]: /index.php/All/?p=1558
 [3]: /index.php/All/?p=1568
 [4]: http://epmframework.codeplex.com/