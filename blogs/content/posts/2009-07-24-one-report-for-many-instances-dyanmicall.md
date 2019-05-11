---
title: Dynamic Data Sources in SSRS
author: Ted Krueger (onpnt)
type: post
date: 2009-07-24T16:03:11+00:00
ID: 523
url: /index.php/datamgmt/datadesign/one-report-for-many-instances-dyanmicall/
views:
  - 16559
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
tags:
  - ssrs

---
This as many of my blogs, is another DBA task and typically isn't required in a user released reporting situation. I have used it a few times for very specialized reports that the user community runs, so it's possible that you may also be able to use it there. One thing I stress is the use of unsafe assembly in this write and the examples I just put together in order to write the blog for everyone. Security on assemblys might be a good follow up blog. 

Two things I'm going to show you today. First is how to scan the network for instances using a SQLCLR UDF. The second is how to use that listing result set in a report so you can quickly run one report like performance monitoring reports for several instances and databases. This basically by making use of dynamic data sources by means of parameters in the connection strings. 

This does two major things for you right away. 

1) It removes uneccessary clutter in creating folders of identical reports pointing to different data sources.

2) It makes backing these reports up much easier and much easier to maintain changes

So the first taks is scanning the network for SQL Server instances

This can be done with the following SQLCLR UDF. 

```csharp
using System;
using System.Data;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using Microsoft.SqlServer.Server;
using System.Collections;

public partial class UserDefinedFunctions
{
    [SqlFunction(FillRowMethodName = "FillRow",TableDefinition = "InstanceName nvarchar(500)", DataAccess=DataAccessKind.Read)]
    public static IEnumerable InstanceFinder()
    {
        System.Data.Sql.SqlDataSourceEnumerator instance = System.Data.Sql.SqlDataSourceEnumerator.Instance;
        System.Data.DataTable dt = instance.GetDataSources();
        return dt.Rows;
    }

    public static void FillRow(Object obj, out SqlString InstanceName)
    {
        DataRow r = (DataRow)obj;
        InstanceName = new SqlString(r[0].ToString());
    }

};
```
Create this and deploy it to your DBA restricted database. As most of my readers know that is always a secured database named DBA (go figure!).

Now call the UDF to search the network for SQL Servers. 

```sql
SELECT * FROM InstanceFinder();
```

Now to use that in SSRS we have to do a few things. Get a project ready and name it "DBAs Rule". Add a shared data source named DBA. This points to the UDF we're going to use. Next add a blank report named, "SQL DB Check.rdl"

Create a new dataset named "ServerListing" like below

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//srv_1.gif" alt="" title="" width="463" height="380" />
</div>

Save that by hitting OK and now let's create a parameter that our dataset will populate. Name the parameter "ServerName". Select "From Query" for available values and find ServerListing in the list. select the only value available for "Value field" and "Label field".

Now back in the Data tab create another dataset. Name this one DBListing. We need a new data source now sense our goal here is to connect to whatever SQL Server the UDF finds. So click the drop down for Data Source and hit create new. Name this DS NoInitialCatalog. That's as meaningful of a name as we can get as that is the key to how we do this. To create this type of connection string all we do is specify the Data Source itself without a initial catalog. For the connection string we need to use our parameter for the server as well. All we do is add it in there as an expression to accomplish that.

like so...

="Data Source=" & Parameters!ServerName.Value

and should appear like this in the prompts...

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//srv_2.gif" alt="" title="" width="474" height="408" />
</div>

Save all of this and then in the text for the dataset use

```sql
Select [Name] From sys.databases
```

This step is import so don't forget to do it. In order for SSRS to know what you are returning from the query on NoIntialCatalog, you need to define it sense validation is out of it's control here and the column will not prefill for you. So in the Fields tab go ahead and enter a field name, "Name" with a type of databases field and value of Name.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//srv_3.gif" alt="" title="" width="474" height="408" />
</div>

Now to actually show this in action we need something on the report so drag a table over and remove the extra columns but the first. Drag over the "Name" from the dataset, "DBListing" and preview the report. 

The report is obviously going to be slow loading. You're scanning the network and we all know how annoying hitting that drop down in SSMS and selecting browse network is. To speed this up I actually modified it in most of my DBA related reports. I used my scans that I talk about [here][1] and use a simple query over the real-time scan. The scan on load is handy and in a few reports that I do searches I still use it but for speed and security, I use the results from my scan SSIS to run most of them.

So after that runs you should end up with the list, select a instance and hit View Report. After that your database listing should return. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//srv_4.gif" alt="" title="" />
</div>

To go farther, you linked the results of the database listing to another parameter like the instance listing. Then run your analysis off of that per database.

 [1]: /index.php/DataMgmt/DBAdmin/scan-network-for-sql-server-instances