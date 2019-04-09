---
title: Idera Diagnostic Manager for End Users
author: Kevin Conan
type: post
date: 2012-03-12T11:46:00+00:00
ID: 1552
excerpt: |
  Do you have a group of developers who as soon as their application has any issues they call you because it must be the database server?  Do you have managers who really want to know if all the jobs are running successfully?  Yeah, me too.
  That's one of&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/start-of-a-series-on/
views:
  - 8523
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - diagnostic manager
  - idera
  - sql server

---
Do you have a group of developers who as soon as their application has any issues they call you because it must be the database server?  Do you have managers who really want to know if all the jobs are running successfully?  Yeah, me too.

That's one of the really cool things with Diagnostic Manager.  You can setup security for Active Directory Groups, Active Directory Logins and SQL Logins for read only access in the Console.

 

<div class="image_block">
  <a href="/media/users/kconan/DM Security.jpg?mtime=1331558267"><img src="/wp-content/uploads/users/kconan/DM Security.jpg?mtime=1331558267" alt="" width="1206" height="627" /></a>
</div>

 

(Click Administration in the bottom left corner, then Application Security on the left side)

Now End Users can install the Idera Diagnostic Manager Console to find out the health of the monitored SQL Instances, Job History, blocking, etc. on their own.

<span style="font-family: Georgia, serif; font-size: 10pt; background-color: white; line-height: 14.25pt;">At my company, we have dozens of End Users with Diagnostic Manager installed and it does not appear to be having a noticeable impact on the monitored SQL Instances.</span>