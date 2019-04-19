---
title: Shared Connections – New level of efficiency in SSIS 2012
author: Ted Krueger (onpnt)
type: post
date: 2011-12-06T12:08:00+00:00
ID: 1414
excerpt: |
  Using the new feature, Shared Connection Managers is a great step in the direction of making development more efficient and manageable.  This is a new feature in SQL Server 2012 SSIS and a welcome one.
  Shared Connection Managers are created for each pr&hellip;
url: /index.php/datamgmt/datadesign/shared-connections-new-level-of/
views:
  - 7132
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - SSIS

---
Using the new feature Shared Connection Managers is a great step in the direction of making development more efficient and manageable.  This is a new feature in SQL Server 2012 SSIS and a welcome one.

Shared Connection Managers are created for each project in SQL Server Data Tools.  Each connection is then available to any package that is contained in that project.  The first performance gain is in development.  Prior to this change, when developing groups of SSIS packages, connections  were contained within each package.  This meant for a person creating multiple packages to work together, the connections needed to be recreated in each package.  We can see the impact that this change can have on development efficiency right away.

**Taking a look**

To create a Shared Connection Manager, open SQL Server Data Tools and create a new project.  Once the project is created, right click Connection Managers and click New Connection Managers.

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-63.png?mtime=1323036357"><img src="/wp-content/uploads/blogs/DataMgmt/-63.png?mtime=1323036357" alt="" width="418" height="220" /></a>
</div>

 

The new connection wizard has not changed much.  Follow the steps to create the connection as you would normally.  In this example, ADO.NET connections were created.

Once you've created a connection, you will see the connections start to show in the Connection Managers window.

 

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-64.png?mtime=1323036357"><img src="/wp-content/uploads/blogs/DataMgmt/-64.png?mtime=1323036357" alt="" width="494" height="56" /></a>
</div>

 

Note the reference of the connection as “(project)”.

These project level connections are now available to use within any package that is contained in the project.  You can still create connections at the package scope.  In many cases, multiple packages are becoming more popular to ensure reusability and ease of maintainability.  This new feature ensures that SSIS development is truly looking to make us more efficient.  More time designing and testing equates to stable and more accurately development SSIS packages.