---
title: 'Error: The GetBytes function can only be used on columns of type Text, NText, or Image'
author: SQLDenis
type: post
date: 2010-06-03T13:41:57+00:00
ID: 793
url: /index.php/datamgmt/datadesign/error-the-getbytes-function-can-only-be/
views:
  - 10798
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - pitfall
  - tip

---
This is just a quick post in case you run into this problem. This is an interesting error and it had a coworker worry about table corruption. This coworker connected to a database on a fairly new server, he then executed a query like the following

```sql
SELECT *
FROM SomeTable
```

And here is the error message he got

<div style="border:1px solid black;background-color:#444;color:white;margin:0 20px;padding:0 5px 0 5px;">
  An error occurred while executing batch. Error message is: Invalid attempt to GetBytes on column 'BookDate'.<br /> Error: The GetBytes function can only be used on columns of type Text, NText, or Image.
</div>

Then I decided to run the same query from my machine and it all worked without a problem. From here it is pretty easy since the problem must be with the client software. And after we checked the version of SSMS we noticed that he connected to the 2008 instance with a 2005 SSMS client. He then connected with SSMS 2008 and it all worked again. What is interesting is that if you use Query Analyzer then you don't get an error and it just works

Lesson learned...it is not the server..it is you!

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22