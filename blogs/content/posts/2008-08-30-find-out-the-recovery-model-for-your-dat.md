---
title: Find Out The Recovery Model For Your Database
author: SQLDenis
type: post
date: 2008-08-30T12:18:16+00:00
url: /index.php/datamgmt/datadesign/find-out-the-recovery-model-for-your-dat/
views:
  - 12464
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - administration
  - database
  - sql
  - sql server

---
You want to quickly find out what the recovery model is for your database but you don&#8217;t want to start clicking and right-clicking in SSMS/Enterprise Manager to get that information. This is what you can do, you can use databasepropertyex to get that info. Replace &#8216;msdb&#8217; with your database name

<pre>select databasepropertyex('msdb','Recovery') </pre>

What if you want it for all databases in one shot? No problem here is how, this will work on SQL Server version 2000

<pre>SELECT name,DATABASEPROPERTYEX(name,'Recovery') 
 from sysdatabases</pre>

For SQL Server 2005 and up, you should use the following command

<pre>select name,recovery_model_desc
from sys.databases</pre>

I have also added this to our SQL Server admin Wiki here: [Find Out The Recovery Model For Your Database][1]
  
Make sure to check out the other wiki entries

If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum

 [1]: http://wiki.ltd.local/index.php/Find_Out_The_Recovery_Model_For_Your_Database
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22