---
title: Messing with your friendly DBA on April Fools' Day
author: SQLDenis
type: post
date: 2013-03-29T10:27:00+00:00
ID: 2056
excerpt: |
  April Fools' Day is a day when people play practical jokes and hoaxes on each other. Why not trying to play some practical jokes on your friendly DBA :-)
  
  The first thing we are going to do is to spoof the host and program name. This is easy to do. Click on Connect, choose Database Engine, you will see the following box
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/messing-with-your-friendly-dba/
views:
  - 28155
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - prank
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
April Fools' Day is a day when people play practical jokes and hoaxes on each other. Why not trying to play some practical jokes on your friendly DBA 🙂

The first thing we are going to do is to spoof the host and program name. This is easy to do. Click on Connect, choose Database Engine, you will see the following box

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/AprilFools1.PNG?mtime=1364551429"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/AprilFools1.PNG?mtime=1364551429" width="411" height="301" /></a>
</div>

Click on options >>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/AprilFools2.PNG?mtime=1364551448"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/AprilFools2.PNG?mtime=1364551448" width="420" height="485" /></a>
</div>

Click on the Additional Connection Parameters tab and paste in the following

Application Name=TOAD;Workstation ID=LarryEllison-PC

Now you can verify that what you have entered is returned from SQL Server

```sql
SELECT host_name,program_name 
FROM  sys.dm_exec_sessions
WHERE session_id = @@spid
```



<pre>host_name	    program_name
LarryEllison-PC	    TOAD</pre>

If the DBA monitors the connection he might notice.....if not, time for plan B

## Kick it up a notch....or two

Time to become real evil 🙂
  
Find out what the biggest table is in your company. Create a database with the same name on your local instance and also create the same table. Now it is time to create a panic.
  
Open another connect dialog box, add the real server in the Server Name box, for example I entered PDWSQLServer2015

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/AprilFools3.PNG?mtime=1364551852"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/AprilFools3.PNG?mtime=1364551852" width="413" height="306" /></a>
</div>

Click on options >>, click on the Additional Connection Parameters tab and paste in the following _**Data Source=localhost**_

Just to verify, run the following query

```sql
SELECT @@SERVERNAME
```

That returns you the local servername. Look what you see everywhere else (highlighted in yellow)

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/AprilFools4.PNG?mtime=1364552737"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/AprilFools4.PNG?mtime=1364552737" width="713" height="440" /></a>
</div>

I see PDWSQLServer2015 everywhere else.
  
Now run your query which returns 0 rows

```sql
SELECT * FROM HugeTable
```

Call your DBA to stop by and then ask him if he deleted all 3 billion rows from this table? Look at his face..let him run `sp_spaceused 'HugeTable'`. Once the panic sets in tell him he has be pranked.......

Of course there is a chance that all your permissions will be taken away.....