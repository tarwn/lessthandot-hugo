---
title: Choosing an Analysis Engine
author: Steve Hughes (DataOnWheels)
type: post
date: 2012-06-21T16:46:00+00:00
ID: 1655
excerpt: 'With the introduction of the SQL Server PowerPivot for Excel and SharePoint in SQL Server 2008 R2, Microsoft gave us another analysis engine we could use with our data. While not embedded into the SQL Server stack at the time, it was clear that in-memor&hellip;'
url: /index.php/datamgmt/ssas/choosing-an-analysis-solution/
views:
  - 18474
rp4wp_auto_linked:
  - 1
categories:
  - SSAS

---
With the introduction of the SQL Server PowerPivot for Excel and SharePoint in SQL Server 2008 R2, Microsoft gave us another analysis engine we could use with our data. While not embedded into the SQL Server stack at the time, it was clear that in-memory technologies are the next generation of analysis in Microsoft. 

### **xVelocity** formerly known as Vertipaq

When it was initially released, the in-memory data solution was called Vertipaq. With the release of SQL Server 2012, the Vertipaq engine was rebranded to xVelocity which includes all of Microsoft’s in-memory technologies. In the case of SQL Server and PowerPivot, Vertipaq is called “xVelocity in-memory analytics engine”, which I will refer to as xVelocity for the duration of this blog.

### The Engine Options

Now that we have covered the xVelocity impact and name change, I want to call out the three core data analysis engines Microsoft has made available through SQL Server. _(Note: I am referring to these as engines to differentiate between end user tools and those tools which store and organize data for use. Here an engine is used to store and organize data for analysis.)_ Microsoft’s original data analysis engine was OLAP Services which has grown into Analysis Services – Multidimensional Model. Next came PowerPivot followed by Analysis Services –Tabular Model.

#### Analysis Services – Multidimensional Model

Being the original analysis and aggregation engine delivered by Microsoft, it has the most mature tools and largest following. While all three options have a heavy memory requirement, the Multidimensional model uses memory for caching. The actual database is stored in the file system and optimized for indexing and retrieval. Currently, the largest cube in the world is well over 20 Terabytes and continues to grow. However, to support these large cubes, storage systems must be configured to handle large amounts of random reads. The best techniques used today include short-stroking moving hard disk media (using only a percentage of the available disk space, usually the outer ring) and solid state disk drives. In order to be a high performing solution, the database needs to be deployed to properly configured storage with sufficient server memory to handle large amounts of cache.

Beyond the storage difference, the multidimensional model natively supports MDX which is a powerful scripting language for complex queries. This language is supported in Excel, Reporting Services and a number of other reporting and dashboard tools. Furthermore, the multidimensional model is the only option that supports actions, many-to-many relationships, and writeback. You should also be aware that this model supports role-playing dimensions which is not currently supported in either of the in-memory options. If you have a business need for any of these options, then multidimensional models are the correct engine for your solution. 

#### PowerPivot

The next engine released by Microsoft was the PowerPivot add-in for SharePoint and Excel. This allows users to build an in-memory data store from disparate data sources in Excel and deploy it for shared use in SharePoint. One of the key goals behind this solution was the ability to create an analysis tool without needing the overhead of moving data to new server location via ETL. 

PowerPivot allows a user to add data into their PowerPivot tables from a variety of data sources including SQL Server, Oracle, Excel, text and even SSRS reports. Once the data has been “loaded” into the PowerPivot tables, the user can build relationships between the tables allowing for a “cube-like” experience without building a cube or ETL process. Once built and deployed to SharePoint, you can build reports against a PowerPivot data source in the same manner you would build a report against the multidimensional model. 

PowerPivot does support calculations and uses DAX as its native query language. This is the same query language used in the Tabular model and can be considered the query language for Microsoft’s in-memory data stores.
  
Finally, it is important to note that the size limit for a PowerPivot database is 2 GB. This is an “artificial” restriction that allows it to be uploaded into SharePoint. While this does hold a large amount of data because of the compression capability of xVelocity, it is still smaller in scope than either the multidimensional or tabular model options.

#### Analysis Services – Tabular Model

In SQL Server 2012, Microsoft introduced the tabular model. This is the “grown up” version of PowerPivot. The tabular model can be built with Visual Studio or migrated from PowerPivot for Excel. The tabular model is also an in-memory data store built on xVelocity. However, the tabular model is limited by the memory available on the server as opposed to the fixed 2 GB limit in PowerPivot. The primary design difference is that the tabular model supports partitions which support scoped data refresh reducing the update time for the model. Beyond partitioning and database size, the capabilities in the tabular model and PowerPivot 2012 version are very similar.

### Choosing the Right Engine

Now that you have an idea of each type of engine made available for analysis, you will need to choose which engine or what mix of engines will be appropriate for your environment. I would encourage you to evaluate the utilization of the data, the size of the data, and the quality of the data as your guides. 

First, if you want to take advantage of Power View, you will need to use an in-memory option. Power View requires the data source to use DAX and that is only supported by tabular models and PowerPivot at this time. Because the drivers used by Reporting Services can reference the in-memory data from either source using MDX, you should have no other usage issues in reporting. 

The next consideration is the size. If you have a large set of data you will need to look at multidimensional or tabular models in Analysis Services. Either of these options supports larger data sets than PowerPivot. 

Finally, if your data is not very clean or very complex, you will need to move the data via ETL first. However, after it is moved you will be able to use any of the engines to work with that data. I would definitely encourage you to move the data into a star schema model so that it can be easily used by all analytic engines.

As you start working with the solutions, you will likely find tabular models and PowerPivot easier to use. However, as your system mature and you have a more complex calculations or data structures such as many-to-many you will be better served working with the multidimensional model. 

### Wrap Up

A solid BI solution within any company will likely be a combination of each of these engines based on the business need. A natural progression could be a business user creates a PowerPivot source that is deployed to SharePoint. This eventually can get upgraded to tabular to support additional growth in users and data volume. From there, you can build out a multidimensional model to support more complex structures as the requirements grow. The one thing that is true about BI is that it will always change. So planning for change and involving the business in the process will help the BI solution be a central part of the business without becoming that database that no one uses.

Keep your options open and use what is available to you to deliver the best solution you can to your users. With Microsoft’s BI offerings you have a multiple engine options as well as a diverse set of visualization tools in SQL Server, SharePoint and Office. In the end, if your BI solution is not usable or correct, it is not the right solution.

For more information, check out Microsoft’s Business Intelligence site which has information on all of Microsoft’s offerings as well as the MSDN topic on Comparing Tabular and Multidimensional Solutions.