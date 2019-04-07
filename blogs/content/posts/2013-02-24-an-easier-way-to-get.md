---
title: 'An easier way to get SQL Server startup parameters: the sys.dm_server_registry dmv'
author: SQLDenis
type: post
date: 2013-02-24T22:51:00+00:00
ID: 2011
excerpt: |
  When you install SQL Server, Setup will write a set of default startup options to the Windows registry. You can use the startup options to specify an alternate master database file, master database log file, or error log file.
  
  Here is what the defaul&hellip;
url: /index.php/datamgmt/dbadmin/an-easier-way-to-get/
views:
  - 11067
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dmv
  - dynamic management view
  - registry
  - sql server 2012
  - startup options

---
When you install SQL Server, Setup will write a set of default startup options to the Windows registry. You can use the startup options to specify an alternate master database file, master database log file, or error log file.

Here is what the default options are according to Books on line

<div class="tables">
  <table>
    <tr>
      <th>
        Default startup options
      </th>
      
      <th>
        Description
      </th>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>-d </strong><span class="parameter">master_file_path</span>
        </p>
      </td>
      
      <td>
        <p>
          The fully qualified path for the master database file (typically, C:Program FilesMicrosoft SQL ServerMSSQL.<span class="parameter">n</span>MSSQLDatamaster.mdf). If you do not provide this option, the existing registry parameters are used.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>-e </strong><span class="parameter">error_log_path</span>
        </p>
      </td>
      
      <td>
        <p>
          The fully qualified path for the error log file (typically, C:Program FilesMicrosoft SQL ServerMSSQL.<span class="parameter">n</span>MSSQLLOGERRORLOG). If you do not provide this option, the existing registry parameters are used.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          <strong>-l </strong><span class="parameter">master_log_path</span>
        </p>
      </td>
      
      <td>
        <p>
          The fully qualified path for the master database log file (typically C:Program FilesMicrosoft SQL ServerMSSQL.<span class="parameter">n</span>MSSQLDatamastlog.ldf). If you do not specify this option, the existing registry parameters are used.
        </p>
      </td>
    </tr>
  </table>
</div>

If you want to get the startup parameters for SQL Server, you can either read the registry or open up SQL Server Configuration Manager and look at the Startup Parameters tab, here is what it looks like on my laptop

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/StartupParameters.PNG?mtime=1361752440"><img alt="SQL Server Configuration Manager: Startup Parameters" title="SQL Server Configuration Manager: Startup Parameters" src="/wp-content/uploads/blogs/DataMgmt/Denis/StartupParameters.PNG?mtime=1361752440" width="417" height="481" /></a>
</div>

Did you know that you can do this in an easier way on SQL Server 2012? No, I am not talking about using the undocumented `xp_regread` stored procedure. A new dynamic management view was introduced, this view is sys.dm\_server\_registry.

Now if I want to see the parameters, all I need is

sql
SELECT registry_key, value_name, value_data
FROM sys.dm_server_registry
WHERE registry_key LIKE N'%MSSQLServerParameters';
```

Output will be something like the following, I made value\_data shorter and left out registry\_key from the output so that it would fit on one line

<pre>value_name	value_data
SQLArg0	        -dC:MSSQLDATAmaster.mdf
SQLArg1	        -eC:MSSQLLogERRORLOG
SQLArg2	        -lC:MSSQLDATAmastlog.ldf</pre>

There is more that you can do, let&#8217;s see I want to know some stuff about SQL agent

sql
SELECT  value_name, value_data
FROM sys.dm_server_registry
WHERE registry_key LIKE N'%SQLServerAgent%';
```

output

<pre>value_name	        value_data
ObjectName	        NT ServiceSQLSERVERAGENT
ImagePath	        "C:MSSQLBinnSQLAGENT.EXE" -i MSSQLSERVER
Start	                2
DependOnService	        MSSQLSERVER 
ErrorLoggingLevel	3
JobHistoryMaxRows	1000
JobHistoryMaxRowsPerJob	100
WorkingDirectory	C:MSSQLJOBS</pre>

To see all that this dmv returns, execute the following

sql
SELECT registry_key, value_name, value_data
FROM sys.dm_server_registry
```

It returns 99 rows on my laptop, most of the stuff is TCP/IP related.