---
title: SQL Server sprawl, heard it twice in one day
author: SQLDenis
type: post
date: 2009-08-05T15:47:40+00:00
excerpt: 'Last week on Wednesday I attended a Quest seminar which was hosted by a couple of Quest people, one of them being Brent Ozar. On my way to New York City I was reading a couple of chapters of SQL Server 2008 Administration In Action written by Rod Colled&hellip;'
url: /index.php/datamgmt/dbprogramming/sql-server-sprawl-heard-it-twice-in-one/
views:
  - 16935
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql server sprawl

---
Last week on Wednesday I attended a Quest seminar which was hosted by a couple of Quest people, one of them being [Brent Ozar][1]. On my way to New York City I was reading a couple of chapters of [SQL Server 2008 Administration In Action][2] written by Rod Colledge. I was totally blown away by what I read, this is probably the best SQL Server admin book I have ever read. I will write up a review in a week or so, I still have to finish the book. 

In this book there is a mention of SQL Server sprawl, interesting I have never heard of this term. After Brent Ozar started his first session he also had SQL Server sprawl on one of his slides. Now, just like me until then you might not know what SQL Server sprawl means but you certainly know what it is after I describe it. So SQL Server sprawl could be a server that sits in a closet and nobody knows what it does or who owns it but there is a sticker on it that says “do not turn off”. Or it might be an instance installed on a desktop under someone desk and it is used to generate production reports. You might have dozens of these and most probably these boxes are not maintained or backed up regularly. By now I am sure that you have a situation like this at your current job or you have worked somewhere where they had such a mess. 

If you need to find out all the instances that are on your network then check out this fine post by [onpnt][3]: [Scan network for SQL Server instances][4]. In that post you will learn how you can use SQL ping to find these instances

What is the solution to SQL Server sprawl? Virtualization is one of the solutions mentioned, this is probably fine for smaller databases but I don’t see my databases going the virtual route any time soon, as a matter of fact we just purchased new servers for our SQL Servers and soon I will be running SQL server 2008 only 🙂

If you would like to read a sample chapter of SQL Server 2008 Administration In Action then visit this link http://www.manning.com/colledge/ There are 2 sample chapters available right now. The book should be available August 10 and you can already preorder it from [Amazon][2]. I will have my review up in a week or two.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][5] forum or our [Microsoft SQL Server Admin][6] forum**<ins></ins>

 [1]: http://www.brentozar.com/
 [2]: http://www.amazon.com/gp/product/193398872X?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=193398872X
 [3]: /index.php/All/?disp=authdir&author=68
 [4]: /index.php/DataMgmt/DBAdmin/scan-network-for-sql-server-instances
 [5]: http://forum.lessthandot.com/viewforum.php?f=17
 [6]: http://forum.lessthandot.com/viewforum.php?f=22