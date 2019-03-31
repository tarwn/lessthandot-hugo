---
title: Getting the list of parameters from a stored procedure by using sqlCmd.Parameters or INFORMATION_SCHEMA.parameters
author: SQLDenis
type: post
date: 2012-02-14T13:11:00+00:00
excerpt: If you want to see what parameters a stored procedure is using then you can accomplish this in a couple of different ways. You can use sys.parameters or INFORMATION_SCHEMA.parameters from within SQL Server itself, you can also SqlCommandBuilder from within ADO.NET
url: /index.php/datamgmt/datadesign/getting-the-list-of-parameters/
views:
  - 9386
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - parameters
  - stored procedure

---
If you want to see what parameters a stored procedure is using then you can accomplish this in a couple of different ways. You can use sys.parameters or INFORMATION_SCHEMA.parameters from within SQL Server itself, you can also SqlCommandBuilder from within ADO.NET

Let&#8217;s say you have a procedure name prTest in a database named Test2

<pre>CREATE PROCEDURE prTest
@id int,@SomeDate date,@Somechar CHAR(1) OUTPUT
AS
SELECT GETDATE(),@id ,@SomeDate ,@Somechar</pre>

Here is how you would do it with c# and ADO.NET

<pre>using System;
using System.Data;
using System.Data.SqlClient;


namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            string yourConnStr = "Data Source=localhost;Initial Catalog=Test2;Integrated Security=SSPI;";
            using (SqlConnection sqlConn = new SqlConnection(yourConnStr))
            using (SqlCommand sqlCmd = new SqlCommand("prTest", sqlConn))
            {
                sqlConn.Open();
                sqlCmd.CommandType = CommandType.StoredProcedure;
                SqlCommandBuilder.DeriveParameters(sqlCmd);
                Console.WriteLine(sqlCmd.Parameters.Count.ToString());
                    foreach (SqlParameter p in sqlCmd.Parameters) {

                        Console.WriteLine(p.ParameterName.ToString() + "t" 
                                + p.Direction.ToString() + "t" + p.DbType.ToString() );
                        }
                Console.ReadLine();
               
                
        }
    }
}</pre>

Output

<pre>4
@RETURN_VALUE   ReturnValue     Int32
@id             Input           Int32
@SomeDate       Input           Date
@Somechar       Input           AnsiStringFixedLength</pre>

As you can see, we get 4 parameters back not 3, this is because the return value is also included. sqlCmd.Parameters.Count returns 4 and when we loop over the sqlCmd.Parameters collection we are displaying the name, direction and data type of the parameter

If you want to do something similar from within SQL Server, you can use the INFORMATION_SCHEMA.parameters view

<pre>SELECT parameter_name, ordinal_position,parameter_mode,data_type 
FROM INFORMATION_SCHEMA.parameters
WHERE SPECIFIC_NAME = 'prTest'</pre>

Here is the output

<div class="tables">
  <table>
    <tr>
      <th>
        parameter_name
      </th>
      
      <th>
        ordinal_position
      </th>
      
      <th>
        parameter_mode
      </th>
      
      <th>
        data_type
      </th>
    </tr>
    
    <tr>
      <td>
        @id
      </td>
      
      <td>
        1
      </td>
      
      <td>
        IN
      </td>
      
      <td>
        int
      </td>
    </tr>
    
    <tr>
      <td>
        @SomeDate
      </td>
      
      <td>
        2
      </td>
      
      <td>
        IN
      </td>
      
      <td>
        date
      </td>
    </tr>
    
    <tr>
      <td>
        @Somechar
      </td>
      
      <td>
        3
      </td>
      
      <td>
        INOUT
      </td>
      
      <td>
        char
      </td>
    </tr>
  </table>
</div>

As you can see INFORMATION_SCHEMA.parameters does not return a row for the return value.

You can also use sys.parameters and join that with sys.types 

<pre>SELECT s.name AS parameter_name,
	   parameter_id AS ordinal_position,
	   CASE is_output WHEN 0 THEN 'IN' ELSE 'INOUT' END Parameter_Mode,
	   t.name AS data_type 
FROM sys.parameters s
JOIN sys.types t ON s.system_type_id = t.user_type_id
WHERE object_id = object_id('prTest')</pre>

Here is the same output as with INFORMATION_SCHEMA.parameters

<div class="tables">
  <table>
    <tr>
      <th>
        parameter_name
      </th>
      
      <th>
        ordinal_position
      </th>
      
      <th>
        parameter_mode
      </th>
      
      <th>
        data_type
      </th>
    </tr>
    
    <tr>
      <td>
        @id
      </td>
      
      <td>
        1
      </td>
      
      <td>
        IN
      </td>
      
      <td>
        int
      </td>
    </tr>
    
    <tr>
      <td>
        @SomeDate
      </td>
      
      <td>
        2
      </td>
      
      <td>
        IN
      </td>
      
      <td>
        date
      </td>
    </tr>
    
    <tr>
      <td>
        @Somechar
      </td>
      
      <td>
        3
      </td>
      
      <td>
        INOUT
      </td>
      
      <td>
        char
      </td>
    </tr>
  </table>
</div>

I think INFORMATION\_SCHEMA.parameters is a little easier to use, another benefit is that INFORMATION\_SCHEMA.parameters also exists on other platforms