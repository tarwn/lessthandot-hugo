---
title: To BUILTINAdmin or not to BUILTINAdmin
author: Ted Krueger (onpnt)
type: post
date: 2009-11-19T13:42:26+00:00
ID: 635
url: /index.php/datamgmt/datadesign/to-builtin-admin-or-not-to-builtin-admin/
views:
  - 27097
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
A while back I wrote, “[To SA or not to SA][1]”. That blog touched my security side that I take very seriously in my database server landscape. I'm glad to have seen so many read the blog in hopes it brought attention to the SA account where it may have been overlooked. In a reply to one of the comments I touched on another default setting for SQL Server regarding the BUILTIN Administrators group. BUILTINAdministrators is created by default on windows operating systems. This group has little to no limitations on the OS and application installations on the server.

So to put it as slightly as I did with SA...

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/NoEntry_admin.gif" alt="" title="" width="292" height="301" />
</div>

If you are on SQL Server 2008 and upgraded by using a fresh install, you do not have to worry about the BUILTIN Administrators group. As SQL Server has evolved and the security has become more advanced, security has been tightened out of the box. I applaud the SQL Server 2008 teams for these changes. Taking this and many other bad practices out of the default settings has pushed SQL Server even farther into the enterprise group.

Most database installations are not easily upgraded of course. I know a few DBAs I communicate with often that even have compatibility levels pre-dating 80 in their respected environments. My own landscape is composed 90% of SQL Server 2005 and plans for 2008 across the board are not slated until Q2, 2010. So if you have SQL Server 2005 or are restricted in use to pre-2008, the BUILTINAdmin group is still of much concern.

## _Taking a look..._

To check your BUILTIN Administrators group and the server role you can do few things.

Open SSMS, connect to a 2005 or 2000 instance and expand the Security node.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/admin_1.gif" alt="" title="" width="293" height="79" />
</div>

Double click the group and select the Server Roles window.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/admin_2.gif" alt="" title="" width="421" height="332" />
</div>

Note the sysadmin checkbox and it being checked. This is from an instance installed with no pre-configurations in mind. 

To do this in T-SQL we can run one of a few system procedures or query off the system tables.

Option 1)

sql
sp_helpsrvrole 'sysadmin'
```

This will show you all members of the sysadmin server roles. Very handy audit tool and one I reported to SOX auditors often with. 

For query abilities, Denis Gobo also has a handy query on the syslogins here
  
http://wiki.ltd.local/index.php/Find\_Out\_Server\_Roles\_For\_a\_SQL\_Server\_Login

In Denis's Wiki entry there is a query at the end that gives us exactly what we need here also

sql
SELECT [name],sysadmin,bulkadmin
FROM master..syslogins
WHERE sysadmin =1 or bulkadmin =1
```

This will show us everyone in the sysadmin role. 

## _So what's the problem?_

The primary problem with leaving the BUILTINAdmin group in the sysadmin role is it has no control. This leads into most windows server installations that have Domain Admins as local administrators on the servers. This brings you to the point of having all Domain Admins as sysadmin's on your database servers. Several problems arrise from this sitaution. Database creations without control, server connections that can cause problems and security gaps to other instances, monitoring concerns and many more uncontrolled issues.

## _Solution..._

There is only one real solution and it starts with the DBA. Here are a few things as a DBA you should think about while either installing a fresh instance or upgrading.

  1. After installing SQL Server pre-2008, the first thing is to remove BUILTINAdmin from the sysadmin role. It is ok to leave the group as public
  2. When upgrading and transferring security accounts, determine if the BUILTINAdmin group is truly required. Prevent moving the group to 2008 when the practice has been removed.
  3. Implement scanning for new instance installations that leave these security gaps open.
  4. Make it a practice to monitor your security. This includes instances you have already tightened up. Automate scripts to send out reports of the groups and roles on your SQL Servers for review. I do this with SSRS minimum of once a week
  5. Take a sound mind while installing SQL Server and take security seriously! Point of failure one on most installations is when you allow everything to take complexity out of the initial security settings.

 [1]: /index.php/DataMgmt/DBAdmin/to-sa-or-not-to-sa