---
title: Procedure expects parameter error from SqlCommand
author: Ted Krueger (onpnt)
type: post
date: 2010-12-27T15:00:42+00:00
ID: 983
excerpt: 'I was recently asked for some help with a very strange situation involving SQL Server and a .NET application using SqlCommand calls.  It was thought to be a problem with SQL Server and in particular, sp_execute not being formed correctly.'
url: /index.php/datamgmt/dbprogramming/sqlcommand-parameter-expected/
views:
  - 13432
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - .net
  - 'c#'
  - slqcommand
  - sql server

---
I was recently asked for some help with a very strange situation involving SQL Server and a .NET application using SqlCommand calls. It was thought to be a problem with SQL Server and in particular, sp_execute not being formed correctly.

**Let's go through the steps to this point**

The developer did exactly the right steps in troubleshooting the problem. Once the application was failing due to SQL Server return errors, Profiler was enlisted to determine the exact transaction that was being sent to SQL Server. The transaction was found to be sent without specifying the parameters thought to be formed

```sql
sp_executesql N'dbo.uspGetEmployeeManagers',N'@EmployeeID INT',@EmployeeID=1
```

When running this, the error returned is

> Msg 201, Level 16, State 4, Procedure uspGetEmployeeManagers, Line 0
  
> Procedure or function 'uspGetEmployeeManagers' expects parameter '@EmployeeID', which was not supplied.

Looking at the statement closer and verifying with BOL sp_executesql syntax, the parameter mapping is not completely set. The proper statement should be called as follows

```sql
exec sp_executesql N'dbo.uspGetEmployeeManagers @EmployeeID',N'@EmployeeID INT',@EmployeeID=1
```

Note the @EmployeeID added to the procedure name based.

In order to recreate the problem entirely, the following code was used.

```csharp
string str = ""; 
SqlConnection conn = new SqlConnection("Data Source=localhost;Initial Catalog=AdventureWorks;Integrated Security=SSPI;"); 
conn.Open();
SqlCommand cmd = new SqlCommand("dbo.usp_Deltest", conn); 
cmd.CommandType = 
CommandType.StoredProcedure; 
cmd.Parameters.Add(
new SqlParameter("@EmployeeID", "1")); 
cmd.Parameters.Add(
new SqlParameter("@ManagerID", "1")); 
SqlDataReader reader = cmd.ExecuteReader(); 
while(reader.Read()) 
{
str = reader[0].ToString();
}
reader.Close();
```

This test application appeared to work as it should and the sp_executesql was sent as expected. After exhausting attempts to force the not well-formed statement on SQL Server, sections of the .NET code itself were looked at closer. In order to test different scenarios, certain lines were commented out to change the way the code was being handled.

Specifically, when the command type setting for stored procedure was commented out, the exact situation was successfully recreated. Unfortunately the BOL entry for SQLCommand.CommandType wasn't much help here other than this section

> When you set the CommandType property to StoredProcedure, you should set the CommandText property to the name of the stored procedure. The command executes this stored procedure when you call one of the Execute methods.
> 
> The Microsoft .NET Framework Data Provider for SQL Server does not support the question mark (?) placeholder for passing parameters to a SQL Statement or a stored procedure called with a CommandType of Text. In this case, named parameters must be used. For example: 
> 
> sql
SELECT * FROM Customers WHERE CustomerID = @CustomerID
```

Again, not a very effective section to the exact problem but it does allow us to come to the conclusion that without the CommandType being set, the parameters are essentially ignored in the procedure call from ADO.NET.

**Conclusion**

If you ever run across the error that the procedure you are calling expects a certain parameter or you see an sp_executesql statement with the same error but the SQL Server profiled transaction appears as it does from this posting, check to ensure you are setting the CommandType and using parameters correctly.

The best lesson learned in this problem was, involving both SQL Server and .NET individuals in the steps to troubleshoot problems such as this is a great way to identify, research and resolve problems quickly. Make an effort to involve your team to make the length of time much smaller when it comes to the situations.