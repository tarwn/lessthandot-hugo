---
title: Suppress string or binary data would be truncated messages with the ANSI WARNINGS setting
author: SQLDenis
type: post
date: 2009-02-09T15:13:45+00:00
url: /index.php/datamgmt/dbprogramming/mssqlserver/surpress-string-or-binary-data-would-be/
views:
  - 81525
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip
  - trick

---
String or binary data would be truncated. How can I suppress that message?

This is a frequent enough question on various forums, I answered this one also today and decided to create a quick blog post about this.

Run this code

<pre>create table bla(id varchar(2))
go

insert bla values ('123')</pre>

You will get the following message:
  
Server: Msg 8152, Level 16, State 14, Line 1
  
String or binary data would be truncated.
  
The statement has been terminated.

But what if you don&#8217;t care about if data is truncated, what if you want to store only what fits? You could do something like this

<pre>insert bla values (left('123',2))</pre>

But since most programmers are lazy they prefer not to change code (and introduce bugs)

Here is one way to do it without changing code but by setting ANSI Warnings to off

<pre>SET ANSI_WARNINGS  OFF

insert bla values ('123')

SET ANSI_WARNINGS  ON --set it back on so code following this won't be messed up</pre>

Now I am not saying that you should do this because setting ANSI warning off also does this: When ON, divide-by-zero and arithmetic overflow errors cause the statement to be rolled back and an error message is generated. When OFF, divide-by-zero and arithmetic overflow errors cause null values to be returned

Lookup [SET ANSI_WARNINGS][1] in BOL before setting it to off and messing up some other code.

 [1]: http://msdn.microsoft.com/en-us/library/ms190368.aspx