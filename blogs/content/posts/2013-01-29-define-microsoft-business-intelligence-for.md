---
title: Define Microsoft Business Intelligence for me?
author: Ted Krueger (onpnt)
type: post
date: 2013-01-29T20:45:00+00:00
ID: 1947
excerpt: 'What is Microsoft Business Intelligence?  I’m not asked this question very often because it’s typically an assumption.  As we know, assumptions can lead to misguidance or misrepresentation of a topic. This topic has been defined in so many different way&hellip;'
url: /index.php/datamgmt/datadesign/define-microsoft-business-intelligence-for/
views:
  - 19027
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Data Modelling and Design
  - Microsoft SQL Server

---
<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-215.png?mtime=1359498894"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-215.png?mtime=1359498894" width="150" height="165" align="left" /></a>
</div>

What is Microsoft Business Intelligence? I’m not asked this question very often because it’s typically an assumption. As we know, assumptions can lead to misguidance or misrepresentation of a topic. This topic has been defined in so many different ways; I ask it in almost every interview I perform that has the word, “Data” in it. The reasoning for asking that question isn’t directly to say a person is wrong or right but to see the range they’ve taken on in knowing all the factors that lead to end solutions in data. 

 

First, I should probably answer the question as it is, from my own experience with Microsoft Business Intelligence. 

_Microsoft Business Intelligence is a set of tools, features and services that help business visualize data. The evolution of data in which it transforms from raw data to an analytical view or visualization of data, is the overall process of both data services and business intelligence. In the concept from raw data to analytical or visualization, business intelligence lies on the right side of the model – analytical and visualization. With Microsoft, this encompasses technology such as PowerPivot, Power View, SQL Server Analysis Services, SQL Server Reporting Services, SQL Server Warehouse fundamentals (a cool way of saying, OLAP designed data storage) and pieces of SQL Server Integration Services._

As we can see, my definition does not have much revolving around SQL Server form a performance stand point. The word performance lends itself to many areas, such as database design, SQL Server setup/configuration/maintainability, ETL and scalability. 

A great example of how these two topics relate but can be considered on a different level of focus is the path one or more OLTP designed databases take to an OLAP designed database. With Microsoft, we look to a normal on-premise landscape that consists of one or more transactional databases housed on SQL Server; a push and pull method using SQL Server Integration Services as a high performance extract, transform and load structure; ending with SQL Server Analysis Services in a cube type layout of the data after manipulation or, real time manipulation occurring. 

Seeing this path, we’ve only touched on the Business Intelligence area with the ending on SSAS. Once this task is completed, the real BI landscape sets in with the visualization of the data. During this entire transfer, BI and Performance cross each other in a few areas. To best illustrate this crossing of utilization, we can draw a guide. 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-214.png?mtime=1359498781"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-214.png?mtime=1359498781" width="839" height="224" /></a>
</div>

As we can see from above, there are two areas of overlap, SQL Server Analysis Service and SQL Server Integration Services. This comes in because both have a performance aspect and a BI aspect to how successfully they will be utilized. SQL Server Analysis Services relates directly to how the data is stored and the analytical behavior it takes on pulling calculated fields into a visualization form. A poor OLAP design can create a bottleneck or ineffective ability to utilize SQL Server Analysis Services without added effort on altering the data. This is even more relevant in SQL Server Integration Services. While performance is important in SQL Server Analysis Service, it is brought to an entirely new level in SQL Server Integration Services. The extraction of data requires a high performance method so the sources and destinations can withstand the high volume of requirements. SQL Server Integration Services is also highly accountable for in-process transformation or manipulation of the data. The transformation of the data is also a key area that requires being attentive to how it is done as it relates to performance. 

Once we define what business intelligence is and can be for business, the next step is defining how it relates to implementing and structuring the design of a business intelligence implementation. Taking how we define business intelligence has a direct impact to how we gauge infrastructure and technology utilization, and skills utilization. This leads to cost effectively placing resources accurately as it pertains to budgets, purchasing of the right technology and services. 

**Summary**

With any high level topic or focus point in technology, defining how we utilize it effectively from the correct impact of skills to hardware consumption will define the success of consuming the technology into your business. 

While SQL Server Analysis Services and SQL Server Integration Services share resources from both performance and business intelligence, the two skillsets are unique in mastering their areas. Fully understanding one takes a great deal of discipline and motivation. Falling into a range of working on both can lead to a lack of fully representing the sides as best as they can be.