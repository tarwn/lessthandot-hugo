---
title: Turning on Optimize for Ad hoc Workloads, Ad Hoc Distributed Queries and more on a AWS RDS SQL Server Instance
author: SQLDenis
type: post
date: 2013-03-21T15:39:00+00:00
ID: 2048
excerpt: |
  There are several advanced options that you can modify in SQL Server.If you want to turn on optimize for ad hoc workloads in SQL Server, one way is to run the following scripts
  
  EXEC sys.sp_configure N'optimize for ad hoc workloads', N'1'
  RECONFIGURE&hellip;
url: /index.php/datamgmt/dbadmin/turning-on-optimize-for-ad/
views:
  - 23767
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin
tags:
  - amazon web services
  - aws
  - cloud
  - rds

---
There are several advanced options that you can modify in SQL Server. If you want to turn on optimize for ad hoc workloads in SQL Server, you can run the following script

sql
EXEC sys.sp_configure N'optimize for ad hoc workloads', N'1'
RECONFIGURE WITH OVERRIDE
GO
```

If you want to use OPENROWSET, you can run the following

sql
EXECUTE sys.sp_configure'Ad Hoc Distributed Queries', '1'
RECONFIGURE WITH OVERRIDE
GO
```

When running SQL Server on Amazon&#8217;s AWS RDS, you can&#8217;t do it like that. If you try running it, you will get the following error.

Msg 15247, Level 16, State 1, Procedure sp_configure, Line 105
  
User does not have permission to perform this action.
  
Msg 5812, Level 14, State 1, Line 1
  
You do not have permission to run the RECONFIGURE statement.

Here is how you have to do it. From the dashboard click on _DB Parameter Groups_. Click on Create DB Parameter Group, pick the version of SQL Server and fill in the text boxes. Here is what it looks like

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup1.PNG?mtime=1363879041"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup1.PNG?mtime=1363879041" width="824" height="207" /></a>
</div>

Check the DB Parameter Group you just created and click on Edit Parameters

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup2.PNG?mtime=1363879237"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup2.PNG?mtime=1363879237" width="740" height="180" /></a>
</div>

You will see a screen like this pop up

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup3.PNG?mtime=1363879348"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup3.PNG?mtime=1363879348" width="617" height="529" /></a>
</div>

Modify the ones that you are interested in and click Save Changes

Click on DB Instances, Click on the Instance Actions dropdown 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup4.PNG?mtime=1363879526"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup4.PNG?mtime=1363879526" width="357" height="225" /></a>
</div>

Select Modify from this dropdown

You will see a screen like this

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup5.PNG?mtime=1363879698"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup5.PNG?mtime=1363879698" width="654" height="387" /></a>
</div>

In the Parameter Group dropdown, pick the Parameter Group you just created, hit Continue and then Modify DB Instance.
  
In order for this to take effect, you need to reboot your instance
  
Click on DB Instances, click on the Instance Actions dropdown 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup4.PNG?mtime=1363879526"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup4.PNG?mtime=1363879526" width="357" height="225" /></a>
</div>

Select reboot from this dropdown

Once the instance is available again, connect from SSMS, right click on the Server, select Properties and click on the Advanced page. It should now match what you have edited in the DB Parameters Group

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup6.PNG?mtime=1363880116"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/AWS/DBParameterGroup6.PNG?mtime=1363880116" width="474" height="335" /></a>
</div>

That is it for this post, as you can see it is pretty simple to do
  
Also check out my other AWS RDS SQL Server posts: [Trying out Amazon Relational Database Service][1] and [How to read the error log on an Amazon RDS SQL Server instance][2]

 [1]: /index.php/DataMgmt/DBProgramming/trying-out-amazon-relational-database
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/how-to-read-the-error