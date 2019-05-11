---
title: 'Little used SSIS tasks: Data Profiling Task'
author: SQLDenis
type: post
date: 2013-03-28T12:19:00+00:00
ID: 2055
excerpt: "I was in New York City yesterday, hanging out with fellow MVP and blogger Ted Krueger in Union Square. I asked him if he uses the Data Profiling Task task a lot, he said not really. I don't use it a lot either but I decided to show you what you can do w&hellip;"
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/little-used-ssis-tasks-data/
views:
  - 32456
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSIS
tags:
  - ssis

---
I was in New York City yesterday, hanging out with fellow MVP and blogger Ted Krueger in Union Square. I asked him if he uses the Data Profiling Task task a lot, he said not really. I don't use it a lot either but I decided to show you what you can do with it.
  
When you do a lot of ETL type of work it is good to know what kind of data distribution you have, you might want to know how many NULLs you have, the statistics and more.

In order to use the Data Profiling Task in SSIS you need to do a couple of things. First thing you have to do is adding a connection, I decided to use my local machine. Next drop a Data Profiling Task on the Control Flow designer pane.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Data Profiling Task1.PNG?mtime=1364472826"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Data Profiling Task1.PNG?mtime=1364472826" width="173" height="73" /></a>
</div>

Once you do that you will see a red circle with an x, if you hover over this icon, you will see the following text: _The "Destination" property is invalid: Missing destination for profile output._ Double click on the Data Profiling Task, leave DestinationType as FileConnection, click on Destination, in the pop up window, choose New File from the Usage Type option, navigate to the folder where you want the file and give the file a name.

Next we have to look what profile requests we want to include. Here is what is available according to Books On Line

<div class="tables">
  <table>
    <tr>
      <th>
        <p>
          Profiles that analyze individual columns
        </p>
      </th>
      
      <th>
        <p>
          Description
        </p>
      </th>
    </tr>
    
    <tr>
      <td>
        <p>
          Column Length Distribution Profile
        </p>
      </td>
      
      <td>
        <p>
          Reports all the distinct lengths of string values in the selected column and the percentage of rows in the table that each length represents.
        </p>
        
        <p>
          This profile helps you identify problems in your data, such as values that are not valid. For example, you profile a column of United States state codes that should be two characters and discover values longer than two characters.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          Column Null Ratio Profile
        </p>
      </td>
      
      <td>
        <p>
          Reports the percentage of null values in the selected column.
        </p>
        
        <p>
          This profile helps you identify problems in your data, such as an unexpectedly high ratio of null values in a column. For example, you profile a Zip Code/Postal Code column and discover an unacceptably high percentage of missing codes.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          Column Pattern Profile
        </p>
      </td>
      
      <td>
        <p>
          Reports a set of regular expressions that cover the specified percentage of values in a string column.
        </p>
        
        <p>
          This profile helps you identify problems in your data, such as string that are not valid. This profile can also suggest regular expressions that can be used in the future to validate new values. For example, a pattern profile of a United States Zip Code column might produce the regular expressions: d{5}-d{4}, d{5}, and d{9}. If you see other regular expressions, your data likely contains values that are not valid or in an incorrect format.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          Column Statistics Profile
        </p>
      </td>
      
      <td>
        <p>
          Reports statistics, such as minimum, maximum, average, and standard deviation for numeric columns, and minimum and maximum for <span><span class="input">datetime</span></span> columns.
        </p>
        
        <p>
          This profile helps you identify problems in your data, such as dates that are not valid. For example, you profile a column of historical dates and discover a maximum date that is in the future.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          Column Value Distribution Profile
        </p>
      </td>
      
      <td>
        <p>
          Reports all the distinct values in the selected column and the percentage of rows in the table that each value represents. Can also report values that represent more than a specified percentage of rows in the table.
        </p>
        
        <p>
          This profile helps you identify problems in your data, such as an incorrect number of distinct values in a column. For example, you profile a column that is supposed to contain states in the United States and discover more than 50 distinct values.
        </p>
      </td>
    </tr>
  </table>
</div>

The following three profiles analyze multiple columns or relationships between columns and tables.

<div class="caption">
</div>

<div class="tables">
  <table>
    <tr>
      <th>
        <p>
          Profiles that analyze multiple columns
        </p>
      </th>
      
      <th>
        <p>
          Description
        </p>
      </th>
    </tr>
    
    <tr>
      <td>
        <p>
          Candidate Key Profile
        </p>
      </td>
      
      <td>
        <p>
          Reports whether a column or set of columns is a key, or an approximate key, for the selected table.
        </p>
        
        <p>
          This profile also helps you identify problems in your data, such as duplicate values in a potential key column.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          Functional Dependency Profile
        </p>
      </td>
      
      <td>
        <p>
          Reports the extent to which the values in one column (the dependent column) depend on the values in another column or set of columns (the determinant column).
        </p>
        
        <p>
          This profile also helps you identify problems in your data, such as values that are not valid. For example, you profile the dependency between a column that contains United States Zip Codes and a column that contains states in the United States. The same Zip Code should always have the same state, but the profile discovers violations of this dependency.
        </p>
      </td>
    </tr>
    
    <tr>
      <td>
        <p>
          Value Inclusion Profile
        </p>
      </td>
      
      <td>
        <p>
          Computes the overlap in the values between two columns or sets of columns. This profile can determine whether a column or set of columns is appropriate to serve as a foreign key between the selected tables.
        </p>
        
        <p>
          This profile also helps you identify problems in your data, such as values that are not valid. For example, you profile the ProductID column of a Sales table and discover that the column contains values that are not found in the ProductID column of the Products table.
        </p>
      </td>
    </tr>
  </table>
  
  <p>
    I decided to pick a handful of requests. Here is what it looks like.
  </p>
  
  <div class="image_block">
    <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Data Profiling Task3.PNG?mtime=1364472845"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Data Profiling Task3.PNG?mtime=1364472845" width="716" height="615" /></a>
  </div>
  
  <p>
    As you can see, you need to specify the connection, the table as well as the columns<br /> After you hit okay, run your SSIS package<br /> In order to see what the task generated, you need to use the Data Profile Viewer. You can find it under SQL Server, Integration Services.
  </p>
  
  <div class="image_block">
    <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Data Profiling Task4.PNG?mtime=1364472855"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Data Profiling Task4.PNG?mtime=1364472855" width="234" height="208" /></a>
  </div>
  
  <p>
    Run the tool, click on open and navigate to the file that the Data Profiling Task created. Now you will see something like this
  </p>
  
  <div class="image_block">
    <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Data Profiling Task5.PNG?mtime=1364472864"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Data Profiling Task5.PNG?mtime=1364472864" width="887" height="629" /></a>
  </div>
  
  <p>
    As you can see you get the percentage of NULL values as well as some graphical indication of the distribution.
  </p>
  
  <p>
    What do you think, useful or do you prefer writing your own queries?
  </p>
</div>