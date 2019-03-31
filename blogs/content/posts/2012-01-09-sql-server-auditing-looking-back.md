---
title: 'SQL Server Auditing: Looking Back'
author: SQLArcher
type: post
date: 2012-01-09T08:00:00+00:00
excerpt: |
  Over the past couple of weeks, we have looked at the new auditing feature introduced in SQL Server 2008. This post will finish up with the series and will cover the remaining subjects:
   
  1.SQL Server 2008 vs. SQL Server 2012,
  2.PBM - Last take on aud&hellip;
url: /index.php/datamgmt/dbadmin/sql-server-auditing-looking-back/
views:
  - 11732
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
Over the past couple of weeks, we have looked at the new auditing feature introduced in SQL Server 2008. This post will finish up with the series and will cover the remaining subjects:

1.SQL Server 2008 vs. SQL Server 2012,
  
2.PBM &#8211; Last take on auditing your audits,
  
3.The Auditing Repository.

### SQL Server 2008 vs. SQL Server 2012

The new release of SQL Server is around the corner, and with it there are numerous enhancements to the various components as well as new features. The auditing component is no different. There are a total of 507 audit actions in SQL Server 2012 compared to the 454 in SQL Server 2008 R2. Some of the new actions are more specifically geared to the new SQL Server 2012 Database Containment Feature.

In addition to this, it also introduces filtering to your audits. This is set up on your audit instead of the Server Audit Specification or Database Audit Specification. This will limited what is written to the audit file even more, however there will be the additional CPU overhead to pay for filtering the audits.

Let&#8217;s take a look how to set it up through the SSMS:

First of all, create the audit as previously and navigate as in the screenshot. Note that we will use testdb, check for dbo schema and only actions taking place by users which are defined as a DB_Owner:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/LookingBack/SQL2012_New/Filtered_Audit_GUI.jpg?mtime=1326103445"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/LookingBack/SQL2012_New/Filtered_Audit_GUI.jpg?mtime=1326103445" width="602" height="170" /></a>
</div>

Next we define a Server Audit Specification to Capture Schema access:
  
_Note. This is necessary to capture the events. Previously a Server Audit Specification captured ALL the events across ALL databases._

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/LookingBack/SQL2012_New/Filtered_Audit_Spec_GUI.jpg?mtime=1326103453"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/LookingBack/SQL2012_New/Filtered_Audit_Spec_GUI.jpg?mtime=1326103453" width="457" height="135" /></a>
</div>

To create the same audit using TSQL(which is easier) is as follows:

<pre>USE [master]
GO

CREATE SERVER AUDIT [Filtered]
TO FILE 
(	FILEPATH = N'E:SQLAuditing'
	,MAXSIZE = 10 MB
	,MAX_FILES = 1
	,RESERVE_DISK_SPACE = OFF
)
WITH
(	QUEUE_DELAY = 1000
	,ON_FAILURE = CONTINUE
	
)
WHERE ([database_name]='testdb' AND [schema_name]='dbo' AND [object_name]='tab1' AND [database_principal_name]='dbo')
ALTER SERVER AUDIT [Filtered] WITH (STATE = ON)
GO

USE [master]
GO

CREATE SERVER AUDIT SPECIFICATION [Server_Spec_Filtering]
FOR SERVER AUDIT [Filtered]
ADD (SCHEMA_OBJECT_ACCESS_GROUP)
WITH (STATE = OFF)
GO</pre>

Below is the results of querying an audit file with the filter:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/LookingBack/SQL2012_New/Filtered_Results.jpg?mtime=1326103463"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/LookingBack/SQL2012_New/Filtered_Results.jpg?mtime=1326103463" width="732" height="63" /></a>
</div>

Other additional functionality is OnError:Fail Operation as well as maximum number of files. Maximum number of files indicates the number of files needed to be kept.

### PBM &#8211; Last Take On Auditing Your Audits

Continuing from the last post, below is some policies which you can use with SQL Server 2008/R2/2012.

They are as follows:
  
1. Audit files.
  
2. Database Audits.
  
3. Server Audits.

_Note. I included a ServerVersion condition if you intend to use this with the Enterprise Policy Framework. This will only execute the policies against SQL Server 2008 upwards._

These policies will take a look at the naming convention for the policies as follows:

Database Audits:

1. Name starts with &#8216;DBAudit_%.
  
2. Audit file name starts with &#8216;DBAudit\_Spec\_%&#8217;.
  
3. Audit is enabled.

Server Audits:

1. Name starts with &#8216;SQLAudit_%&#8217;.
  
2. Audit file name starts with &#8216;ServerAudit_%&#8217;.
  
3. Audit is enabled.

Audit Files:

1. Audit file is enabled.
  
2. Destination type is file.
  
3. 100 Maximum files.
  
4. 10 Roll over files
  
5. File size is maximum 5GB.
  
6. File size unit is GB.
  
7. Reserve disk space true.

Below are the create conditions:

<pre>--- Audit_Condition

Declare @condition_id int
EXEC msdb.dbo.sp_syspolicy_add_condition @name=N'Audit_Condition', @description=N'', @facet=N'Audit', @expression=N'&lt;Operator&gt;
  &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
  &lt;OpType&gt;AND&lt;/OpType&gt;
  &lt;Count&gt;2&lt;/Count&gt;
  &lt;Operator&gt;
    &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
    &lt;OpType&gt;AND&lt;/OpType&gt;
    &lt;Count&gt;2&lt;/Count&gt;
    &lt;Operator&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;OpType&gt;AND&lt;/OpType&gt;
      &lt;Count&gt;2&lt;/Count&gt;
      &lt;Operator&gt;
        &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
        &lt;OpType&gt;AND&lt;/OpType&gt;
        &lt;Count&gt;2&lt;/Count&gt;
        &lt;Operator&gt;
          &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
          &lt;OpType&gt;AND&lt;/OpType&gt;
          &lt;Count&gt;2&lt;/Count&gt;
          &lt;Operator&gt;
            &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
            &lt;OpType&gt;AND&lt;/OpType&gt;
            &lt;Count&gt;2&lt;/Count&gt;
            &lt;Operator&gt;
              &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
              &lt;OpType&gt;EQ&lt;/OpType&gt;
              &lt;Count&gt;2&lt;/Count&gt;
              &lt;Attribute&gt;
                &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
                &lt;Name&gt;Enabled&lt;/Name&gt;
              &lt;/Attribute&gt;
              &lt;Function&gt;
                &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
                &lt;FunctionType&gt;True&lt;/FunctionType&gt;
                &lt;ReturnType&gt;Bool&lt;/ReturnType&gt;
                &lt;Count&gt;0&lt;/Count&gt;
              &lt;/Function&gt;
            &lt;/Operator&gt;
            &lt;Operator&gt;
              &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
              &lt;OpType&gt;EQ&lt;/OpType&gt;
              &lt;Count&gt;2&lt;/Count&gt;
              &lt;Attribute&gt;
                &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
                &lt;Name&gt;DestinationType&lt;/Name&gt;
              &lt;/Attribute&gt;
              &lt;Function&gt;
                &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
                &lt;FunctionType&gt;Enum&lt;/FunctionType&gt;
                &lt;ReturnType&gt;Numeric&lt;/ReturnType&gt;
                &lt;Count&gt;2&lt;/Count&gt;
                &lt;Constant&gt;
                  &lt;TypeClass&gt;String&lt;/TypeClass&gt;
                  &lt;ObjType&gt;System.String&lt;/ObjType&gt;
                  &lt;Value&gt;Microsoft.SqlServer.Management.Smo.AuditDestinationType&lt;/Value&gt;
                &lt;/Constant&gt;
                &lt;Constant&gt;
                  &lt;TypeClass&gt;String&lt;/TypeClass&gt;
                  &lt;ObjType&gt;System.String&lt;/ObjType&gt;
                  &lt;Value&gt;File&lt;/Value&gt;
                &lt;/Constant&gt;
              &lt;/Function&gt;
            &lt;/Operator&gt;
          &lt;/Operator&gt;
          &lt;Operator&gt;
            &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
            &lt;OpType&gt;EQ&lt;/OpType&gt;
            &lt;Count&gt;2&lt;/Count&gt;
            &lt;Attribute&gt;
              &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
              &lt;Name&gt;MaximumFiles&lt;/Name&gt;
            &lt;/Attribute&gt;
            &lt;Constant&gt;
              &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
              &lt;ObjType&gt;System.Double&lt;/ObjType&gt;
              &lt;Value&gt;100&lt;/Value&gt;
            &lt;/Constant&gt;
          &lt;/Operator&gt;
        &lt;/Operator&gt;
        &lt;Operator&gt;
          &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
          &lt;OpType&gt;EQ&lt;/OpType&gt;
          &lt;Count&gt;2&lt;/Count&gt;
          &lt;Attribute&gt;
            &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
            &lt;Name&gt;MaximumRolloverFiles&lt;/Name&gt;
          &lt;/Attribute&gt;
          &lt;Constant&gt;
            &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
            &lt;ObjType&gt;System.Double&lt;/ObjType&gt;
            &lt;Value&gt;10&lt;/Value&gt;
          &lt;/Constant&gt;
        &lt;/Operator&gt;
      &lt;/Operator&gt;
      &lt;Operator&gt;
        &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
        &lt;OpType&gt;EQ&lt;/OpType&gt;
        &lt;Count&gt;2&lt;/Count&gt;
        &lt;Attribute&gt;
          &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
          &lt;Name&gt;MaximumFileSize&lt;/Name&gt;
        &lt;/Attribute&gt;
        &lt;Constant&gt;
          &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
          &lt;ObjType&gt;System.Double&lt;/ObjType&gt;
          &lt;Value&gt;5&lt;/Value&gt;
        &lt;/Constant&gt;
      &lt;/Operator&gt;
    &lt;/Operator&gt;
    &lt;Operator&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;OpType&gt;EQ&lt;/OpType&gt;
      &lt;Count&gt;2&lt;/Count&gt;
      &lt;Attribute&gt;
        &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
        &lt;Name&gt;MaximumFileSizeUnit&lt;/Name&gt;
      &lt;/Attribute&gt;
      &lt;Function&gt;
        &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
        &lt;FunctionType&gt;Enum&lt;/FunctionType&gt;
        &lt;ReturnType&gt;Numeric&lt;/ReturnType&gt;
        &lt;Count&gt;2&lt;/Count&gt;
        &lt;Constant&gt;
          &lt;TypeClass&gt;String&lt;/TypeClass&gt;
          &lt;ObjType&gt;System.String&lt;/ObjType&gt;
          &lt;Value&gt;Microsoft.SqlServer.Management.Smo.AuditFileSizeUnit&lt;/Value&gt;
        &lt;/Constant&gt;
        &lt;Constant&gt;
          &lt;TypeClass&gt;String&lt;/TypeClass&gt;
          &lt;ObjType&gt;System.String&lt;/ObjType&gt;
          &lt;Value&gt;Gb&lt;/Value&gt;
        &lt;/Constant&gt;
      &lt;/Function&gt;
    &lt;/Operator&gt;
  &lt;/Operator&gt;
  &lt;Operator&gt;
    &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
    &lt;OpType&gt;EQ&lt;/OpType&gt;
    &lt;Count&gt;2&lt;/Count&gt;
    &lt;Attribute&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;Name&gt;ReserveDiskSpace&lt;/Name&gt;
    &lt;/Attribute&gt;
    &lt;Function&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;FunctionType&gt;True&lt;/FunctionType&gt;
      &lt;ReturnType&gt;Bool&lt;/ReturnType&gt;
      &lt;Count&gt;0&lt;/Count&gt;
    &lt;/Function&gt;
  &lt;/Operator&gt;
&lt;/Operator&gt;', @is_name_condition=0, @obj_name=N'', @condition_id=@condition_id OUTPUT
Select @condition_id

GO


--- DatabaseAudit_Condition

Declare @condition_id int
EXEC msdb.dbo.sp_syspolicy_add_condition @name=N'DatabaseAudit_Condition', @description=N'', @facet=N'DatabaseAuditSpecification', @expression=N'&lt;Operator&gt;
  &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
  &lt;OpType&gt;AND&lt;/OpType&gt;
  &lt;Count&gt;2&lt;/Count&gt;
  &lt;Operator&gt;
    &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
    &lt;OpType&gt;AND&lt;/OpType&gt;
    &lt;Count&gt;2&lt;/Count&gt;
    &lt;Operator&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;OpType&gt;LIKE&lt;/OpType&gt;
      &lt;Count&gt;2&lt;/Count&gt;
      &lt;Attribute&gt;
        &lt;TypeClass&gt;String&lt;/TypeClass&gt;
        &lt;Name&gt;AuditName&lt;/Name&gt;
      &lt;/Attribute&gt;
      &lt;Constant&gt;
        &lt;TypeClass&gt;String&lt;/TypeClass&gt;
        &lt;ObjType&gt;System.String&lt;/ObjType&gt;
        &lt;Value&gt;DBAudit_%&lt;/Value&gt;
      &lt;/Constant&gt;
    &lt;/Operator&gt;
    &lt;Operator&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;OpType&gt;LIKE&lt;/OpType&gt;
      &lt;Count&gt;2&lt;/Count&gt;
      &lt;Attribute&gt;
        &lt;TypeClass&gt;String&lt;/TypeClass&gt;
        &lt;Name&gt;Name&lt;/Name&gt;
      &lt;/Attribute&gt;
      &lt;Constant&gt;
        &lt;TypeClass&gt;String&lt;/TypeClass&gt;
        &lt;ObjType&gt;System.String&lt;/ObjType&gt;
        &lt;Value&gt;DBAudit_Spec_%&lt;/Value&gt;
      &lt;/Constant&gt;
    &lt;/Operator&gt;
  &lt;/Operator&gt;
  &lt;Operator&gt;
    &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
    &lt;OpType&gt;EQ&lt;/OpType&gt;
    &lt;Count&gt;2&lt;/Count&gt;
    &lt;Attribute&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;Name&gt;Enabled&lt;/Name&gt;
    &lt;/Attribute&gt;
    &lt;Function&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;FunctionType&gt;True&lt;/FunctionType&gt;
      &lt;ReturnType&gt;Bool&lt;/ReturnType&gt;
      &lt;Count&gt;0&lt;/Count&gt;
    &lt;/Function&gt;
  &lt;/Operator&gt;
&lt;/Operator&gt;', @is_name_condition=0, @obj_name=N'', @condition_id=@condition_id OUTPUT
Select @condition_id

GO



--- ServerAudit_Condition

Declare @condition_id int
EXEC msdb.dbo.sp_syspolicy_add_condition @name=N'ServerAudit_Condition', @description=N'', @facet=N'ServerAuditSpecification', @expression=N'&lt;Operator&gt;
  &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
  &lt;OpType&gt;AND&lt;/OpType&gt;
  &lt;Count&gt;2&lt;/Count&gt;
  &lt;Operator&gt;
    &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
    &lt;OpType&gt;AND&lt;/OpType&gt;
    &lt;Count&gt;2&lt;/Count&gt;
    &lt;Operator&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;OpType&gt;LIKE&lt;/OpType&gt;
      &lt;Count&gt;2&lt;/Count&gt;
      &lt;Attribute&gt;
        &lt;TypeClass&gt;String&lt;/TypeClass&gt;
        &lt;Name&gt;AuditName&lt;/Name&gt;
      &lt;/Attribute&gt;
      &lt;Constant&gt;
        &lt;TypeClass&gt;String&lt;/TypeClass&gt;
        &lt;ObjType&gt;System.String&lt;/ObjType&gt;
        &lt;Value&gt;SQLAudit_%&lt;/Value&gt;
      &lt;/Constant&gt;
    &lt;/Operator&gt;
    &lt;Operator&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;OpType&gt;LIKE&lt;/OpType&gt;
      &lt;Count&gt;2&lt;/Count&gt;
      &lt;Attribute&gt;
        &lt;TypeClass&gt;String&lt;/TypeClass&gt;
        &lt;Name&gt;Name&lt;/Name&gt;
      &lt;/Attribute&gt;
      &lt;Constant&gt;
        &lt;TypeClass&gt;String&lt;/TypeClass&gt;
        &lt;ObjType&gt;System.String&lt;/ObjType&gt;
        &lt;Value&gt;ServerAudit_%&lt;/Value&gt;
      &lt;/Constant&gt;
    &lt;/Operator&gt;
  &lt;/Operator&gt;
  &lt;Operator&gt;
    &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
    &lt;OpType&gt;EQ&lt;/OpType&gt;
    &lt;Count&gt;2&lt;/Count&gt;
    &lt;Attribute&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;Name&gt;Enabled&lt;/Name&gt;
    &lt;/Attribute&gt;
    &lt;Function&gt;
      &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
      &lt;FunctionType&gt;True&lt;/FunctionType&gt;
      &lt;ReturnType&gt;Bool&lt;/ReturnType&gt;
      &lt;Count&gt;0&lt;/Count&gt;
    &lt;/Function&gt;
  &lt;/Operator&gt;
&lt;/Operator&gt;', @is_name_condition=0, @obj_name=N'', @condition_id=@condition_id OUTPUT
Select @condition_id

GO


---ServerVersion

Declare @condition_id int
EXEC msdb.dbo.sp_syspolicy_add_condition @name=N'ServerVersion', @description=N'', @facet=N'Server', @expression=N'&lt;Operator&gt;
  &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
  &lt;OpType&gt;OR&lt;/OpType&gt;
  &lt;Count&gt;2&lt;/Count&gt;
  &lt;Operator&gt;
    &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
    &lt;OpType&gt;EQ&lt;/OpType&gt;
    &lt;Count&gt;2&lt;/Count&gt;
    &lt;Attribute&gt;
      &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
      &lt;Name&gt;VersionMajor&lt;/Name&gt;
    &lt;/Attribute&gt;
    &lt;Constant&gt;
      &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
      &lt;ObjType&gt;System.Double&lt;/ObjType&gt;
      &lt;Value&gt;10&lt;/Value&gt;
    &lt;/Constant&gt;
  &lt;/Operator&gt;
  &lt;Operator&gt;
    &lt;TypeClass&gt;Bool&lt;/TypeClass&gt;
    &lt;OpType&gt;EQ&lt;/OpType&gt;
    &lt;Count&gt;2&lt;/Count&gt;
    &lt;Attribute&gt;
      &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
      &lt;Name&gt;VersionMajor&lt;/Name&gt;
    &lt;/Attribute&gt;
    &lt;Constant&gt;
      &lt;TypeClass&gt;Numeric&lt;/TypeClass&gt;
      &lt;ObjType&gt;System.Double&lt;/ObjType&gt;
      &lt;Value&gt;11&lt;/Value&gt;
    &lt;/Constant&gt;
  &lt;/Operator&gt;
&lt;/Operator&gt;', @is_name_condition=0, @obj_name=N'', @condition_id=@condition_id OUTPUT
Select @condition_id

GO</pre>

Next are the policies:

<pre>Declare @object_set_id int
EXEC msdb.dbo.sp_syspolicy_add_object_set @object_set_name=N'Audit_Policy_ObjectSet', @facet=N'Audit', @object_set_id=@object_set_id OUTPUT
Select @object_set_id

Declare @target_set_id int
EXEC msdb.dbo.sp_syspolicy_add_target_set @object_set_name=N'Audit_Policy_ObjectSet', @type_skeleton=N'Server/Audit', @type=N'AUDIT', @enabled=True, @target_set_id=@target_set_id OUTPUT
Select @target_set_id

EXEC msdb.dbo.sp_syspolicy_add_target_set_level @target_set_id=@target_set_id, @type_skeleton=N'Server/Audit', @level_name=N'Audit', @condition_name=N'', @target_set_level_id=0
GO

Declare @policy_id int
EXEC msdb.dbo.sp_syspolicy_add_policy @name=N'Audit_Policy', @condition_name=N'Audit_Condition', @policy_category=N'', @description=N'', @help_text=N'', @help_link=N'', @schedule_uid=N'00000000-0000-0000-0000-000000000000', @execution_mode=0, @is_enabled=False, @policy_id=@policy_id OUTPUT, @root_condition_name=N'ServerVersion', @object_set=N'Audit_Policy_ObjectSet'
Select @policy_id
GO

Declare @object_set_id int
EXEC msdb.dbo.sp_syspolicy_add_object_set @object_set_name=N'DatabaseAudit_Policy_ObjectSet', @facet=N'DatabaseAuditSpecification', @object_set_id=@object_set_id OUTPUT
Select @object_set_id

Declare @target_set_id int
EXEC msdb.dbo.sp_syspolicy_add_target_set @object_set_name=N'DatabaseAudit_Policy_ObjectSet', @type_skeleton=N'Server/Database/DatabaseAuditSpecification', @type=N'DATABASEAUDITSPECIFICATION', @enabled=True, @target_set_id=@target_set_id OUTPUT
Select @target_set_id

EXEC msdb.dbo.sp_syspolicy_add_target_set_level @target_set_id=@target_set_id, @type_skeleton=N'Server/Database/DatabaseAuditSpecification', @level_name=N'DatabaseAuditSpecification', @condition_name=N'', @target_set_level_id=0
EXEC msdb.dbo.sp_syspolicy_add_target_set_level @target_set_id=@target_set_id, @type_skeleton=N'Server/Database', @level_name=N'Database', @condition_name=N'', @target_set_level_id=0
GO

Declare @policy_id int
EXEC msdb.dbo.sp_syspolicy_add_policy @name=N'DatabaseAudit_Policy', @condition_name=N'DatabaseAudit_Condition', @policy_category=N'', @description=N'', @help_text=N'', @help_link=N'', @schedule_uid=N'00000000-0000-0000-0000-000000000000', @execution_mode=0, @is_enabled=False, @policy_id=@policy_id OUTPUT, @root_condition_name=N'ServerVersion', @object_set=N'DatabaseAudit_Policy_ObjectSet'
Select @policy_id
GO

Declare @object_set_id int
EXEC msdb.dbo.sp_syspolicy_add_object_set @object_set_name=N'ServerAudit_Policy_ObjectSet', @facet=N'ServerAuditSpecification', @object_set_id=@object_set_id OUTPUT
Select @object_set_id

Declare @target_set_id int
EXEC msdb.dbo.sp_syspolicy_add_target_set @object_set_name=N'ServerAudit_Policy_ObjectSet', @type_skeleton=N'Server/ServerAuditSpecification', @type=N'SERVERAUDITSPECIFICATION', @enabled=True, @target_set_id=@target_set_id OUTPUT
Select @target_set_id

EXEC msdb.dbo.sp_syspolicy_add_target_set_level @target_set_id=@target_set_id, @type_skeleton=N'Server/ServerAuditSpecification', @level_name=N'ServerAuditSpecification', @condition_name=N'', @target_set_level_id=0
GO

Declare @policy_id int
EXEC msdb.dbo.sp_syspolicy_add_policy @name=N'ServerAudit_Policy', @condition_name=N'ServerAudit_Condition', @policy_category=N'', @description=N'', @help_text=N'', @help_link=N'', @schedule_uid=N'00000000-0000-0000-0000-000000000000', @execution_mode=0, @is_enabled=False, @policy_id=@policy_id OUTPUT, @root_condition_name=N'ServerVersion', @object_set=N'ServerAudit_Policy_ObjectSet'
Select @policy_id
GO</pre>

This concludes the PBM monitoring for this series. the conditions can be changed as per your needs, and the name prefixes are only suggestions that will work well with a central repository.

### The Auditing Repository

So you are capturing audits to a remote server, and they are all sitting there and getting backed up occasionally. Current value of the auditing? Zero, NULL.

In this form they&#8217;re not providing you with any insight. Let&#8217;s rather pull it into a database. First off we need one:

<pre>/*
This is just a template to create a table that will host the audit information.
Personally I would recommend to use the following format to name the table especially if it gets centralized:

For database audits:
&lt;DBAudit&gt;_&lt;Action&gt;

For server audits:
&lt;SQLAudit&gt;_&lt;Action&gt;

Then personally I use the following for the specifications:

&lt;ServerAudit&gt;_&lt;Action&gt;
&lt;DBAudit&gt;_&lt;Spec&gt;_&lt;Action&gt;

This makes it easier for naming the tables where I would take the following approach:

&lt;SERVERNAME&gt;_&lt;AUDIT&gt;_&lt;ACTIONS&gt;_&lt;DATABASE_NAME&gt; (OPTIONAL for database audits)
*/

if exists(select name from sys.databases where name = '[AuditingLogs]')
drop database [AuditingLogs];
go

CREATE DATABASE [AuditingLogs];
go

USE [AuditingLogs];
GO;

CREATE TABLE [dbo].[AuditTable_Template](
	[event_time] [datetime2](7) NOT NULL,
	[sequence_number] [int] NULL,
	[action_id] [char](4) NULL,
	[succeeded] [bit] NULL,
	[permission_bitmask] [bigint] NULL,
	[is_column_permission] [bit] NULL,
	[session_id] [int] NULL,
	[server_principal_id] [int] NULL,
	[database_principal_id] [int] NULL,
	[target_server_principal_id] [int] NULL,
	[target_database_principal_id] [int] NULL,
	[object_id] [int] NULL,
	[class_type] [char](2) NULL,
	[session_server_principal_name] [nchar](30) NULL,
	[server_principal_name] [nchar](30) NULL,
	[server_principal_sid] [varbinary](50) NULL,
	[database_principal_name] [nchar](30) NULL,
	[target_server_principal_name] [nchar](30) NULL,
	[target_server_principal_sid] [varbinary](50) NULL,
	[target_database_principal_name] [nchar](30) NULL,
	[server_instance_name] [nvarchar](120) NULL,
	[database_name] [nvarchar](120) NULL,
	[schema_name] [nvarchar](120) NULL,
	[object_name] [nvarchar](120) NULL,
	[statement] [nvarchar](4000) NULL,
	[additional_information] [nvarchar](4000) NULL,
	[file_name] [varchar](260) NULL,
	[audit_file_offset] [bigint] NULL,
	[user_defined_event_id] [bit] NULL,
	[user_defined_information] [nvarchar](4000) NULL,
 CONSTRAINT [PK_AuditTable_Template] PRIMARY KEY CLUSTERED 
(
	[event_time] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO</pre>

This gives us our database, you can just move you files to the correct directory as well as add all the secondary files and file groups you need to sustain this. There is also a table &#8220;template&#8221; with which to create your tables. Each table will be specific to an audit file.

Next we need to get the audit information into the database, below is the script I used and can be modified to fit into either a SQL Job or SSIS package.

<pre>/*
This script contains some insert statements as to how you will go about inserting your captured audit events into a SQL database.
Note that I am using event_time as this is the most unique value that will be written down. I am also reading all the information
into the database as you may need this for regulatory requirements.
*/

USE AuditingLogs;
go


INSERT  INTO AuditingLogs.dbo.[Table]
        SELECT  *
        FROM    sys.fn_get_audit_file(file'*.sqlaudit',
                                      DEFAULT, DEFAULT) gaf
        WHERE   gaf.event_time NOT IN ( SELECT  event_time
                                        FROM    table )


INSERT  INTO AuditingLogs.dbo.[Table]
        SELECT  *
        FROM    sys.fn_get_audit_file('file*.sqlaudit',
                                      DEFAULT, DEFAULT) gaf
        WHERE   gaf.event_time NOT IN ( SELECT  event_time
                                        FROM    table  )


INSERT  INTO AuditingLogs.dbo.[Table]
        SELECT  *
        FROM    sys.fn_get_audit_file('file*.sqlaudit',
                                      DEFAULT, DEFAULT) gaf
        WHERE   gaf.event_time NOT IN ( SELECT  event_time
                                        FROM    table  )</pre>

This is currently very basic, and I included some comments into the scripts to give you an overview of what is going on.

For one of the audits used in demoing the SQL Server Auditing feature, I pulled all the information into a table. As per the sys.fn\_get\_audit\_file we can return the same results from the table. In the following example I&#8217;m using sys.dm\_audit_actions to join to the table to make the information more clear:

<pre>USE AuditingLogs ;
go

SELECT  del.[event_time] ,
        aa.name ,
        aa.action_in_log ,
        del.[session_id] ,
        del.[session_server_principal_name] ,
        del.[server_instance_name] ,
        del.[database_name] ,
        del.[object_name] ,
        del.[statement]
FROM    [dbo].[SQL2012_DBAudit_Deletes] del
        JOIN sys.dm_audit_actions aa ON aa.action_id = del.action_id
ORDER BY del.event_time ASC</pre>

This is the result:

<div class="image_block">
  <a href="/wp-content/uploads/users/sqlarcher/AuditBlog/LookingBack/query_exmaple_results.jpg?mtime=1326109318"><img alt="" src="/wp-content/uploads/users/sqlarcher/AuditBlog/LookingBack/query_exmaple_results.jpg?mtime=1326109318" width="1077" height="114" /></a>
</div>

Currently the database is only hosting the information and it will be nice if Microsoft will add the ability to audit directly to a table in future versions of SQL Server.

### Closing the Chapter

The auditing feature very useful, and is significantly more so than C2 auditing due to the level of granularity. This is also one of the drawbacks; if the auditing is not well planned out it can bring your database or even server to its knees. As with all auditing mechanisms this feature can chew it&#8217;s way through SAN and a lot of thought needs to go into planning this, and if done so, will pay off greatly in the end. With filtering included in SQL Server 2012 it relieves some of this drawbacks in terms of the impact across a SQL Server instance. Overall I think it&#8217;s a great feature, and it still has the ability to improve greatly.

Below are the links to the rest of the series:

[SQL Server Auditing: Introduction][1]
  
[SQL Server Auditing: Creating a Server Specification][2]
  
[SQL Server Auditing: Creating a Database Specification][3]
  
[SQL Server Auditing: Managing Your Audits][4]

_Note. Keep an eye on LessThanDot.com to see some cool things happen to the audit repository!!!_

 [1]: /index.php/DataMgmt/?p=1557
 [2]: /index.php/DataMgmt/?p=1558
 [3]: /index.php/DataMgmt/?p=1568
 [4]: /index.php/DataMgmt/?p=1573