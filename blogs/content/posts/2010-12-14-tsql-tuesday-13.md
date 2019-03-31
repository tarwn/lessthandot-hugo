---
title: 'T-SQL Tuesday #13: What the Business Says Is Not What the Business Wants'
author: Ted Krueger (onpnt)
type: post
date: 2010-12-14T10:09:43+00:00
excerpt: 'Steve Jones (Twitter | Blog), The Voice of the DBA, is hosting the T-SQL Tuesday blogging fest over on SQLServerCentral.com this month.  The topic is one that has been a battle between DBAs, Developers and even managers since the creation of computing in the business itself.  It causes heated arguments, career changes, and trips to your psychiatrist and worst of all, more work and over-thinking that is ever needed for some projects.  What the Business Says Is Not What the Business Wants.'
url: /index.php/datamgmt/datadesign/tsql-tuesday-13/
views:
  - 3792
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - cubes
  - olap
  - reporting
  - sql server
  - t-sql tuesday

---
 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/olap_1.gif" alt="" title="" width="154" height="154" align="left" />
</div></a>Steve Jones ([Twitter][1] | [Blog][2]), The Voice of the DBA, is hosting the T-SQL Tuesday blogging fest over on [SQLServerCentral.com][3] this month. The topic is one that has been a battle between DBAs, Developers and even managers since the creation of computing in the business itself. It causes heated arguments, career changes, trips to your psychiatrist and worst of all, more work and over-thinking that is ever needed for some projects. _What the Business Says Is Not What the Business Wants_.**The common theme**Business is growing, data is growing and your reporting solutions are suffering. The Business comes to your hard working, Grade A Senior DBA and asks, &#8220;We need cubes and cool software to fix this slow and lame reporting issue we have&#8221;.The next steps in your reply to this question are the most critical to your OLTP database servers and countless sleepless nights trying to maintain endless database server problems.**The Business**<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/olap_2.gif" alt="" title="" width="67" height="91" align="right" />
</div>Let’s get something out of the way: Business doesn’t care what you need to do, they want it now and they want exactly what they want. I’m not against third party reporting solutions, cube generators and so on. In fact, some of them are brilliant, intuitive and functional tools that are extremely efficient. I’ve been involved in decisions to purchase some of them and implement them. They were all good experiences and successful for the most part.**Not what they want**<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/olap_3.gif" alt="" title="" width="160" height="126" align="left" />
</div>What the business is venturing into here is OLAP (Online Analytical Processing). OLAP sits on a warehouse (or multiple warehouses). A warehouse in general is a data source in which one or more OLTP (Online Transaction Processing) data sources have been taken and collected into the centralized data source. In most cases these OLTP data sources are denormalized for enhanced performance. Denormalization is the method in which tables that have been normalized to some degree are taken and combined into one larger table. This is all done while still retaining some integrity in keeping the data free of corruption (or making it dirty). In all, denormalizing creates a great deal of redundancy. Redundancy in reporting is normal though. OLAP database servers are meant to handle this type of design with different configurations and hardware to ensure processing is as fast as possible given the designs.Recall the business request of throwing the vendor product into the existing database landscape in order to resolve the reporting issues. The key that is missing is the need for a warehouse to sit behind that reporting solution. Without this critical planning and implementation stage, reporting tools will not become a solution but only worsen the already troubled situation. Now instead of reports that were created and reading from the OLTP sources, there will be cubes processing on-demand, users pulling directly from the OLTP sources with much higher level calculations and resource consumption will start to occur at much greater levels. **Give them what they want**<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/olap_4.gif" alt="" title="" width="109" height="109" align="left" />
</div>Even if you are one voice in a large data team, it is your job to speak up, knowing the problems that will endure from not properly putting the data source in place that the reporting solutions should be utilizing. Yes, implementing a warehouse will take months and a large amount of planning and work. The fact remains, though, that without the proper planning and implementation, your nights will be longer. As hard as it is to sometimes to remember, business looks to you as a database professional to make the right choices to better the business. These types of implementations are a large part of that.

 [1]: http://twitter.com/way0utwest
 [2]: http://www.sqlservercentral.com/blogs/steve_jones/default.aspx
 [3]: http://www.sqlservercentral.com/blogs/steve_jones/archive/2010/12/07/t_2D00_sql-tuesday-_2300_13-_2D00_-what-the-business-says-is-not-what-the-business-wants.aspx