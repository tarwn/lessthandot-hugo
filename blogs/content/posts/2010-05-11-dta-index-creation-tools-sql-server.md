---
title: Trusting Database Engine Tuning Advisor for Query Tuning
author: Ted Krueger (onpnt)
type: post
date: 2010-05-11T13:45:20+00:00
ID: 786
excerpt: So my tip for the day; don't forget DTA as a beneficial tool to be the lazy DBA 
  we all speak of.  It can truly help you even if that is simply taking some code writing 
  shortcuts.  But, believe it as much as you believe the first Google hit you get back 
  when researching a problem.
url: /index.php/datamgmt/datadesign/dta-index-creation-tools-sql-server/
views:
  - 8886
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database engine tuning advisor
  - index
  - maintenance
  - sql server 2005
  - sql server 2008

---
Using Database Engine Tuning Advisor can lend a great deal to a DBA. All you need to do is plug in a query, hit start and viola, instant 99% performance increase estimates. OK, it might not be all that but it can give you some pretty good suggestions for increasing performance. Is this too good to be true? In some cases, yes, it is. 

Let's look at something I just ran across. I do actively use DTA. It's a great tool and it makes tuning extremely large queries quick with reviewing suggests along with execution plans to ensure recommendations are sound. Even better and my primary reason for using DTA is it writes the CREATE statements for you. Remember, a lazy DBA is actually a more efficient DBA. 

This morning however, DTA was being less than helpful. 

A query went by my monitoring tools that sent my disk queue length threw the roof. Well, it went to 8 but as most DBAs know, 3 will make us squirm in our chairs. Once I reviewed the query, it wasn't really all that bad. In fact, I think it was well written by the vendor. Checking the execution plan I could see there was just some covering index needs to ensure parallelism and some key lookup steps were removed. So I opened DTA already knowing pretty much what I wanted to do and ran the query through it. The results were about what I expected but looking closer I saw this 

sql
CREATE NONCLUSTERED INDEX [_dta_index_9_379148396__K1] ON [dbo].[WHDR] 
(
	[WO_ID] ASC
)WITH (SORT_IN_TEMPDB = OFF, IGNORE_DUP_KEY = OFF, DROP_EXISTING = OFF, ONLINE = OFF) ON [PRIMARY]
```

Why is that a problem you might ask? Well, it might not be a problem if you are working on a HEAP table and that typical work order ID is really in need of a nonclustered index. At that point we would want the awesomeness of a clustered index anyhow.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/omg.gif" alt="" title="" width="243" height="243" align="left" />
</div>

Here is the biggest problem. There is a clustered index on the table and that clustered index only consists of the WO_ID. Yes, that would be an extremely big problem as far as performance goes. You would have two indexes doing the same thing and on every change to the table, you would be updating them at the same time. In a large table that cost can show quick and become painful to the users.

So my tip for the day; don't forget DTA as a beneficial tool to be the lazy DBA we all speak of. It can truly help you even if that is simply taking some code writing shortcuts. But, believe it as much as you believe the first Google hit you get back when researching a problem. As Janice Lee ([Twitter][1] | [Blog][2]) says in [Save Me, Google][3], “Google told him to. He was in trouble, he panicked, and he trusted his salvation to Google.” Well, don't fall into the points that Janice put together so well in that blog. Even the tools that are meant to assist us in doing our jobs better can be wrong. They are suggestions that require as much review as anything else going into your database servers.

 [1]: http://twitter.com/janiceclee
 [2]: http://janiceclee.com/
 [3]: http://janiceclee.com/2010/05/10/google-and-suspect-databases/