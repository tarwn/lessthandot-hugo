---
title: Use Filters in SSMS to only see the things that are of interest to you
author: SQLDenis
type: post
date: 2010-02-06T18:13:04+00:00
url: /index.php/datamgmt/dbprogramming/use-filters-in-ssms-to-only-see-the-thin/
views:
  - 5773
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - productivity
  - ssms
  - tip

---
Now that all my server are SQL Server 2008 I use SSMS with SSMS Toolpack and Toad. I don&#8217;t really use Query Analyzer anymore. The other day I found out that you can hide objects you don&#8217;t want to see in SSMS by using filters. 

Let&#8217;s first look at some code. Create a new database named test, create a new schema named Denis and then add 3 tables to the dbo schema and 3 tables to the Denis schema. Just run the code below

<pre>create database test
go

use test
go

create schema Denis
go

create  table Test1(id int);
go
create  table Test2(id int);
go
create  table Test3(id int);
go


create  table Denis.Test1(id int);
go
create  table Denis.Test2(id int);
go
create  table Denis.Test3(id int);
go</pre>

Now in SSMS you will see the following if you expand the tables folder

<div>
  <img src="/wp-content/uploads/blogs/DataMgmt//AllTables.PNG" alt="" title="" width="195" height="199" />
</div>

What if I only want to see the tables that belong to the Denis schema? Here is how you can setup the filter. Right click on the tables folder, select Filter and then Filter settings.

<div>
  <img src="/wp-content/uploads/blogs/DataMgmt//FilterSettings.PNG" alt="" title="" width="403" height="196" />
</div>

That will pop up a window that looks like the image below

<div>
  <img src="/wp-content/uploads/blogs/DataMgmt//Filters.png" alt="" title="" width="559" height="448" />
</div>

For the Schema property select equals for the operator and type Denis as value. Click OK and now you will see the following when you navigate to the tables folder.

<div>
  <img src="/wp-content/uploads/blogs/DataMgmt//FilteredView.PNG" alt="" title="" width="187" height="146" />
</div>

If you want to remove the filter, just right click on the folder, select Filter and then Remove Filter.



If you work in a team and you created several tables that all start with Report then you can create a new filter and just show these objects. This way you don&#8217;t need to see any tables that your team members created. Below is a screen shot of a filter like that.

<div>
  <img src="/wp-content/uploads/blogs/DataMgmt/NewSnip.PNG" alt="" title="" width="538" height="96" />
</div>

Another cool feature is that you can filter on Creation Date, this gives you the ability to filter all that old [Brownfield][1] stuff that nobody maintains anyway because if you change one thing it will break everything else 🙂

So are you using filters in SSMS?

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://en.wikipedia.org/wiki/Brownfield_(software_development)
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22