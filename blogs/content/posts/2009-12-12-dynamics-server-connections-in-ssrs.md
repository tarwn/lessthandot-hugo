---
title: Dynamic SQL Server connections in Reporting Services
author: Ted Krueger (onpnt)
type: post
date: 2009-12-12T12:36:53+00:00
ID: 649
url: /index.php/datamgmt/dbprogramming/dynamics-server-connections-in-ssrs/
views:
  - 16730
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
## _Getting Motivated to make things better..._

I set out tonight to revamp my SSRS DBA portal and reports. Jason Strate ([@StrateSQL][1]) sent me over an index analysis query that he's been working on and it gave me motivation to get the initial page to my portal redone. If you're not on Twitter or connected to [Jason's blog][2], I'd recommend reading and following him.

Part of my 'old' methods I used in my DBA portal on SSRS had been tied into the SQL Server instances scans I do weekly. If you haven't read how I set that up, you can [here][3]. I still like that scan method and will probably be using it for some time to come. I've written many different variations to accomplish the task of scanning the network for instances and that one has always fit into my processes very well so it will be around for some time to come. 

For SSRS and typical DBA reporting though, the need to be dynamic in using one report to look at all of your instances is a pretty nice addition. My last method for doing that was to just query the tables that the scans would populate for me. The problem with this is I had to setup a data source to at least that DBA database and send the request to get the listing that the scan found in the previous execution. This worked very well but I wanted to go a different route and make this more portable and easier for someone to take to their own environment. 

## _Accomplishing the task..._

The only way to really accomplish a programmatic solution other than connecting to a data source and using SQLCLR or a T-SQL method is to write a custom assembly. Once written, you can bring the assembly into SSRS and use it the same way you would call a function from the code section in the report properties.

In C# and VB.NET searching for SQL Server instances is actually very easy. It's just a matter of using the SqlDataSourceEnumerator class and calling up the GetDataSources method. This can be directed into a DataTable and then manipulated pretty much as you need it. In the case of the DBA portal page I want to return the instance found on the network and populate a parameter in order to make the selection of which server to analyze at any given time.

In order to get the data from the DataTable into the parameter I needed to get everything into an Array. This made it clean in populating the parameter that contains multiple values. To do this I used the Array.ConvertAll method. That also gave me a way to ensure conversion to string types and it also gave me an easy way to handle default instances verses named instances. 

Handling default and named instances requires added coding because of the way the table is returned from the GetDataSources method.
  

  
GetDataSources returns the following records for each instance found

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/getsource.gif" alt="" title="" width="732" height="161" />
</div>

Resource: http://msdn.microsoft.com/en-us/library/system.data.sql.sqldatasourceenumerator.getdatasources.aspx

Note the description for InstanceName; Name of the server instance. Blank if the server is running as the default instance.

The way this would have been more useful is if the InstanceName always shows as the <span class="MT_violet">@@SERVERNAME</span> does. Sense named instances require the server name in the connection, there is the need to concatenate the two columns together when the InstanceName was found to have a value.

To get that working we can do the following...

```csharp
public static string DataRowToString(DataRow dr)
        {
            if (Convert.ToString(dr[1].ToString()) != "")
            {
return Convert.ToString(dr[0].ToString()) + "\" + Convert.ToString(dr[1].ToString());
            }
            else
            {
                return Convert.ToString(dr[0].ToString());
            }
        }
```
The DataRowToString is called in the output parameter of Array.ConvertAll.
  

  
The full code of the class library is below.
  


```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Data;

namespace ClassLibrary1
{
    public class InstanceSearch
    {
        public static String[] InstanceFinder()
        {
            System.Data.Sql.SqlDataSourceEnumerator instance = System.Data.Sql.SqlDataSourceEnumerator.Instance;
            System.Data.DataTable dt = instance.GetDataSources();

            DataRow[] dr = new DataRow[dt.Rows.Count];
            dt.Rows.CopyTo(dr, 0);
string[] strinstances = Array.ConvertAll(dr, new Converter<DataRow, String>(DataRowToString));
            return strinstances;
        }

        public static string DataRowToString(DataRow dr)
        {
            if (Convert.ToString(dr[1].ToString()) != "")
            {
return Convert.ToString(dr[0].ToString()) + "\" + Convert.ToString(dr[1].ToString());
            }
            else
            {
                return Convert.ToString(dr[0].ToString());
            }
        }
    }
}
```

To create this library yourself all you need to do is follow the steps outlined

  1. Open Visual Studio .NET and create a new project
  2. Select C# and then Class Library
  3. Rename the Class that is created by default to InstanceSearch
  4. Copy / Paste the code above into the class and save it
  5. Build the library once done
  6. Copy the dll file out of the debug or release folder where you created your project into the following directory (change the letter for VS versions higher than 8)
  
      
    C:Program FilesMicrosoft Visual Studio 8Common7IDEPrivateAssemblies

Now that you have your custom assembly ready open another session of Visual Studio .NET and create a new report project. 

In your new report project add a new item by right clicking the project name and going through add to new item

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/addnewitem.gif" alt="" title="" width="308" height="322" />
</div>

For now we can ignore the Data area of this report and any data sources that you might be used to initially setting up. Ensure you are in the layout and in the menu strip, select Report and open Report Properties. In the report properties window, select the References tab. Click the brose button to add the assembly to the assembly listing by opening the browse dialog. Navigate to the private assembly folder and double click the assembly we created earlier.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/browserassembly.gif" alt="" title="" width="431" height="441" />
</div>

Once the assembly is added to the development environment you are ready to call it.
  

  
Add a new parameter to the report and make the following changes
  


  1. Data Type = String
  2. Allow blank value
  3. Select Non-Queried under the "Available values" section
  4. Make Label <blank></blank>
  5. rary1.InstanceSearch.InstanceFinder() as an expression in the Value option

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/parm_rs_1.gif" alt="" title="" width="598" height="610" />
</div>

Once this is done, you can run the report to start the search for all the instances on the network. The report will be slow on load. Don't be surprised about this and shocked. This is the same method that is taken when click the browse the network options from BIDS and SSMS.
  

  
When the report renders you should see in the parameter the dropdown of all the servers available to query

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/results_scan.gif" alt="" title="" width="394" height="231" />
</div>

You can see how useful this can be to a DBA or even other groups in the IT department. The custom assembly doesn't have to be restricted to a instance search. It can look for other software installations or other pieces of Active Directory that may interest administrators. There are many options available to the dynamic nature of starting a report path off this way.
  

  
To extend this into database and other objects, check out
  
/index.php/DataMgmt/DBProgramming/one-report-for-many-instances-dyanmicall

In that blog I show the SQLCLR method but further into the building of the data source so you can completely go dynamic and move from instance to instance when analyzing your servers.

I hope you have fun with it and get some use out of this method.

 [1]: http://twitter.com/stratesql
 [2]: http://stratesql.com/
 [3]: /index.php/DataMgmt/DBAdmin/scan-network-for-sql-server-instances