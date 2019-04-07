---
title: The History of SQL Server Integration Services
author: Ted Krueger (onpnt)
type: post
date: 2010-10-11T09:53:52+00:00
ID: 920
excerpt: 'SQL Server has taken on many levels of growth over the years as a relational database management system.  SQL Server has taken on roughly twelve major releases; code names were adopted 10 major releases ago.    During the time of these releases, Microsoft has enriched the features that ship with the SQL Server Engine itself.  These features started with SQL Server 7.0 and the release of OLAP; code name, Plato.  With Plato, came a direction to fulfill the goal of bringing SQL Server to an enterprise level.  Also packaged with SQL Server 7.0 was Data Transformations Services (DTS).  DTS was a milestone as it provided the ability to broaden the range of abilities to work natively with SQL Server and Bulk type operations.  Job scheduling capabilities were also increased in the ability to work more freely with more complex tasks.  These tasks were housed directly in SQL Server (with respect to outside binaries and data stores).  SQL Server 7.0 made a foundation for the years to come.'
url: /index.php/datamgmt/datadesign/history-of-ssis/
views:
  - 7934
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql server 2008 r2
  - sql server history
  - sql server integration services
  - ssis

---
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sql60_logo.gif" alt="" title="" width="300" height="236" align="left" />
</div>

The history of any product is important to fully understanding the product itself. With the knowledge of the foundation of a products birth, we can start to learn the depth of where it has come as it has aged over the years. SQL Server and all its features are no different. We may find ourselves disliking a product before taking full advantage of the abilities offered. 

SQL Server has had many levels of growth over the years as a relational database management system. **SQL Server** has taken on roughly twelve major releases; code names were adopted 10 major releases ago. Throughout these releases, Microsoft has enriched the features that ship with the SQL Server Engine itself. These features started with SQL Server 7.0 and the release of OLAP; code name, Plato. With Plato, came a direction to fulfill the goal of bringing SQL Server to an enterprise level. Also packaged with SQL Server 7.0 was Data Transformations Services (DTS). DTS was a milestone as it provided the ability to broaden the range of abilities to work natively with SQL Server and Bulk type operations. Job scheduling capabilities were also increased in the ability to work more freely with more complex tasks. These tasks were housed directly in SQL Server (with respect to outside binaries and data stores). SQL Server 7.0 provided a foundation for the years to come. 

> SQL Server 2000 DTS
  
> http://technet.microsoft.com/en-us/library/cc917688.aspx

Was SQL Server 7.0 Enterprise Edition truly an enterprise product? The answer to this question has been asked and opinions are always voiced. It was a stepping stone. That is the opinion of the author of this article. Moving into the future from SQL Server 7.0, SQL Server 2000 code name, Shiloh was released. SQL Server 2000 had with it changes for the better within the performance of SQL Server itself. DTS, **SQL Server Reporting Services** (SSRS) and **SQL Server Analysis Services** (SSAS) were built up and enriched. Although SSRS at this time was an add-in, the reporting foundation also had begun to develop. Still the questioned remained if SQL Server 2000 had met the level that the path SQL Server was meant to follow of truly being an enterprise database management system.

SQL Server 2005 will probably stick in most database professional’s minds for decades and versions to come. Why? SQL Server 2005 released with it major database engine changes and also a truly enriched features listing. SQL Server Integrated Services (SSIS) was one of these features. SSIS was the calling that ETL for SQL Server needed to move forward. To be direct: SSIS was in no way an upgraded version of DTS. DTS was extinct from SQL Server 2005 all together. Legacy components were developed to manage large and small installations that based ETL on DTS in order to make transitions less complicated. These components (**Microsoft SQL Server 2000 DTS Designer Components**) gave us the ability to edit and execute DTS on SQL Server 2005 to some extent. This development was meant as an interim while conversions to SSIS were completed. DTS run-time components are a deprecated feature. At the time of this article’s creation and the current release of SQL Server 2008 R2, DTS run-time in 32-bit was still available.

> SQL Server 2000 DTS Designer Components
  
> http://www.microsoft.com/downloads/details.aspx?FamilyID=d09c1d60-a13c-4479-9b91-9e8b9d835cdc
  
> SQL Server Integrated Services Home Page
  
> http://www.microsoft.com/sqlserver/2008/en/us/integration.aspx 

SQL Server 2008 R2 is the most recent release of SSIS. We will stay with this being mentioned as SSIS 2008 as no new features were added to the major release of 2008 R2. With the 2008 release of SQL Server, some changes had also been made to enhance the abilities of the ETL platform. Caching and full utilization of multi-core platforms are a few large steps in the performance abilities of SSIS. With these and many other changes, DTS to SSIS itself, there are sometimes painful steps to make in learning the best practices and best method of utilizing them. This is part of most major releases or changes to existing releases.

Why is this all important?
  
The history of any product is important to know and follow. This assists in learning and understanding the current architecture of the product. Granted, learning SSIS does not mean we should run out and install SQL Server 7.0 in order to write your first DTS package. Remember, DTS and SSIS are completely different ETL platforms and set of tools, components and tasks. SSIS however is something that we can start from 2005 release and move forward with.

Take the process of deciding which version to purchase. Even with a base landscape falling primarily under SQL Server 2005 Engines, SQL Server 2008 may be the best choice. Lookup Caching options themselves may be the turning point on that choice. Knowing the differences and the changes over the history of the product will help us make better decisions like these.

Knowing the history of SSIS can also play an important step in development of SSIS itself. For example; the ActiveX Script Task remains a task in SSIS 2008. This task allows the execution of sequentially interpreted scripting languages (or non-compiled) VBScript and Jscript. This task may be viewed as beneficial to many developing SSIS packages as there is exposure to other resources and more complex processing of external objects. However, with SSIS 2005, VSA (Visual Studio for Applications) environment was available, making developing in VB.NET as an option. This exposed the power of VSA and exposing a large number of namespaces to SSIS. Looking to the future, SSIS 2008 changed to utilizing VSTA (Visual Studio Tools for Applications). VSTA provided SSIS with a heightened level of reference to external resources and addition of the development language, C#.

> Migrating Scripts to VSTA http://msdn.microsoft.com/en-us/library/bb522527.aspx 

So as we can see, understanding the history and changes of a product may assist in decision making from major purchases down to choosing the more efficient task or component while developing under platforms like SSIS.

> History of SQL Server Integrated Services – Main product documentation
  
> SSIS 2005 http://technet.microsoft.com/en-us/library/ms141026(SQL.90).aspx
  
> SSIS 2008 http://technet.microsoft.com/en-us/library/ms141026.aspx
  
> SSIS Team Home Pages
  
> Technet http://technet.microsoft.com/en-us/sqlserver/cc510302.aspx
  
> MSDN http://msdn.microsoft.com/en-us/sqlserver/cc511477.aspx