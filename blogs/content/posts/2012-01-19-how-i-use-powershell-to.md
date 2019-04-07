---
title: How I use PowerShell to generate table output for blog posts from query results
author: SQLDenis
type: post
date: 2012-01-19T14:04:00+00:00
excerpt: If you are a blogger, you want to spend time on writing the content not on formatting tables and prettifying syntax so that it looks nice. The syntax is taken care of because we use the GeSHi syntax highlighter. formatted output is another story
url: /index.php/datamgmt/datadesign/how-i-use-powershell-to/
views:
  - 12453
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
tags:
  - blogging
  - powershell
  - scripting

---
If you are a blogger, you want to spend time on writing the content not on formatting tables and prettifying syntax so that it looks nice. The syntax is taken care of because we use the [GeSHi][1] syntax highlighter. formatted output is another story

If you want to show the output of a query in a html table you have a couple of options. Let&#8217;s first take a look at a simple query

<pre>SELECT TOP 10 name,create_date
FROM msdb.sys.procedures
ORDER BY create_date</pre>

You can of course use the PRE tag to format that somewhat, here is what it looks like

<pre>name	                             create_date
sp_sqlagent_is_srvrolemember	     2010-02-06 04:12:12.100
sp_verify_category_identifiers	     2010-02-06 04:12:12.100
sp_verify_proxy_identifiers	     2010-02-06 04:12:12.107
sp_verify_credential_identifiers     2010-02-06 04:12:12.110
sp_verify_subsystems	             2010-02-06 04:12:12.130
sp_verify_subsystem_identifiers	     2010-02-06 04:12:12.137
sp_verify_login_identifiers	     2010-02-06 04:12:12.140
sp_verify_proxy	                     2010-02-06 04:12:12.143
sp_add_proxy	                     2010-02-06 04:12:12.150
sp_delete_proxy	                     2010-02-06 04:12:12.157</pre>

What I want is a table, here is what that looks like

<div class="tables">
  <table>
    <tr>
      <th>
        name
      </th>
      
      <th>
        create_date
      </th>
    </tr>
    
    <tr>
      <td>
        sp_sqlagent_is_srvrolemember
      </td>
      
      <td>
        9/29/2010 10:13:10 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_category_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:10 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_proxy_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:10 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_credential_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:10 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_subsystems
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_subsystem_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_login_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_proxy
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_add_proxy
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_delete_proxy
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
  </table>
</div>

In order to accomplish that, I can grab the query result and put TD and TR tags around the columns and rows. I can also do something like the following

<pre>SELECT TOP 10 '<tr&gt;<td&gt;',name,'</td&gt;<td&gt;',create_date,'</td&gt;</tdr&gt;'
FROM msdb.sys.procedures
ORDER BY create_date</pre>

But what if it is a stored proc like xp_fixeddrives? You can of course also use Excel, paste the results in there, add columns and put the TD and TR tags in those columns. but there is a better/easier/faster way. You can use PowerShell to do this for you, you can use the [ConvertTo-Html Cmdlet][2]

Here is a simple powershell script that accepts an instance parameter, runs the query and then generates a html file as output

<pre>Param($InstanceName)

$SqlConnection = New-Object System.Data.SqlClient.SqlConnection
$SqlConnection.ConnectionString = "Server=$InstanceName;Database=master;Integrated Security=True"


$SqlCmd = New-Object System.Data.SqlClient.SqlCommand
$SqlCmd.Connection = $SqlConnection
$SqlCmd.CommandTimeout = 0
$SqlCmd.Connection.Open()



$SqlCmd.CommandText = "SELECT TOP 10 name,create_date
                        FROM msdb.sys.procedures
                        ORDER BY create_date"
                        
$SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
$SqlAdapter.SelectCommand = $SqlCmd
$DataSet = New-Object System.Data.DataSet
$SqlAdapter.Fill($DataSet)


$FilePath    = "C:PowerShellOutput" + $InstanceName+ "_formattedOutput.htm"
$Title = $InstanceName+ " Formatted Results for Blog"
$DataSet.Tables[0]  | convertto-html -property name, create_date -title $Title &gt; $FilePath   </pre>

Save the file as FormatOutput.ps1, I have it saved in my D:Powershell folder, the file will save the output in my C:PowerShellOutput folder

The script has a parameter that is the name of the SQL Server instance that you want to run the query against, you specify that with -InstanceName after the filename and you then supply the name of your instance, in my case it is localhost

Run the PowerShell script like this

<pre>powershell.exe "D:PowershellFormatOutput.ps1" -InstanceName localhost</pre>

You can run it from a command prompt or from the PowerShell Integrated Scripting Environment (ISE) 

Now go in your folder where the file has been created, if you open it in notepad or if you do view source when you open it in a browser, you will see something like this

<pre><!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"&gt;
<html xmlns="http://www.w3.org/1999/xhtml"&gt;
<head&gt;
<title&gt;localhost FreeSpace</title&gt;
</head&gt;<body&gt;
<table&gt;
<colgroup&gt;
<col/&gt;
<col/&gt;
</colgroup&gt;
<tr&gt;<th&gt;name</th&gt;<th&gt;create_date</th&gt;</tr&gt;
<tr&gt;<td&gt;sp_sqlagent_is_srvrolemember</td&gt;<td&gt;9/29/2010 10:13:10 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_category_identifiers</td&gt;<td&gt;9/29/2010 10:13:10 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_proxy_identifiers</td&gt;<td&gt;9/29/2010 10:13:10 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_credential_identifiers</td&gt;<td&gt;9/29/2010 10:13:10 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_subsystems</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_subsystem_identifiers</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_login_identifiers</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_proxy</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_add_proxy</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_delete_proxy</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
</table&gt;
</body&gt;</html&gt;</pre>

All I have to do now is create this part of the code in my blogpost

<pre><div class="tables"&gt;
<table&gt;
<! Paste stuff here  !&gt;
</table&gt;
</div&gt;</pre>

I then copy and paste the rows from the html table between the table tags on line 3 in the previous code example

Here is what I end up with

<pre><div class="tables"&gt;
<table&gt;
<tr&gt;<th&gt;name</th&gt;<th&gt;create_date</th&gt;</tr&gt;
<tr&gt;<td&gt;sp_sqlagent_is_srvrolemember</td&gt;<td&gt;9/29/2010 10:13:10 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_category_identifiers</td&gt;<td&gt;9/29/2010 10:13:10 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_proxy_identifiers</td&gt;<td&gt;9/29/2010 10:13:10 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_credential_identifiers</td&gt;<td&gt;9/29/2010 10:13:10 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_subsystems</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_subsystem_identifiers</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_login_identifiers</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_verify_proxy</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_add_proxy</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
<tr&gt;<td&gt;sp_delete_proxy</td&gt;<td&gt;9/29/2010 10:13:11 AM</td&gt;</tr&gt;
</table&gt;
</div&gt;</pre>

And that will then look like this

<div class="tables">
  <table>
    <tr>
      <th>
        name
      </th>
      
      <th>
        create_date
      </th>
    </tr>
    
    <tr>
      <td>
        sp_sqlagent_is_srvrolemember
      </td>
      
      <td>
        9/29/2010 10:13:10 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_category_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:10 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_proxy_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:10 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_credential_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:10 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_subsystems
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_subsystem_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_login_identifiers
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_verify_proxy
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_add_proxy
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
    
    <tr>
      <td>
        sp_delete_proxy
      </td>
      
      <td>
        9/29/2010 10:13:11 AM
      </td>
    </tr>
  </table>
</div>

Pretty cool and simple

**Next steps**
  
I am no PowerShell expert and I am sure that the script in this post can be improved, leave me a comment if you see a major mistake

Of course you don&#8217;t want to hard code the query either, you could make the script generic so that you can supply the query as a parameter instead

 [1]: http://qbnz.com/highlighter/index.php
 [2]: http://technet.microsoft.com/en-us/library/ee156817.aspx