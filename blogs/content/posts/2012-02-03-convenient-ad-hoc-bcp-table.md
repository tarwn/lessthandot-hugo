---
title: Convenient ad-hoc bcp table loading
author: Erik
type: post
date: 2012-02-04T00:00:00+00:00
ID: 1514
excerpt: "Many times I am given an Excel file, or have a data source that makes it easy to paste into Excel, and I need to use the data in my database for a one-time purpose of updating or checking other data. It's not worth creating an SSIS package, and it's not&hellip;"
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/convenient-ad-hoc-bcp-table/
views:
  - 23727
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Many times I am given an Excel file, or have a data source that makes it easy to paste into Excel, and I need to use the data in my database for a one-time purpose of updating or checking other data. It's not worth creating an SSIS package, and it's not even worth using a wizard in SSMS. So generally I use bcp as a very fast method.

To do that, I quickly create a staging table in my database, then save the Excel file as tab-delimited.

Then I have to go play with bcp and figure out the syntax yet again, since I don't do it often enough to have made it unthinkingly automatic. Recently I got tired of these repeated lookups and created the following batch file to help out:

<span style="font-family: courier new,courier;">@echo off<br />:top<br />if .%1==. goto end<br />echo Ready to import file to SqlServerName, DB DBName:<br />echo  %1<br />set /P tablename=Type destination table name and press Enter: <br />osql -S SqlServerName -d DBName</span> <span style="font-family: courier new,courier;"></span><span style="font-family: courier new,courier;">-E -Q "truncate table %tablename%"<br />bcp DBName.%tablename% in %1 -S SqlServername -T -c<br />echo.<br />shift<br />goto top<br />:end<br />set tablename=<br />pause</span>

Name the batch file "SqlServerName DBName import replace (drop file).bat".

Now in Windows Explorer, just drag any file you want to import to your database onto the batch file. When prompted, type in the schema and table name you want it loaded to, and press Enter. You'll get a nice pause to see what the result was so you can fix it if not successful. It even supports dropping multiple files, and you'll be asked for a destination table name for each file.

If you find yourself needing to load a text file to a particular table over and over, just make a batch file specific to that text file, and you can drag-and-drop your imports super easily.

You can of course customize the batch file to do anything you want. To prompt for server name, database name, username, password, or anything you like. And you won't have to look up the syntax for bcp one more blasted time!