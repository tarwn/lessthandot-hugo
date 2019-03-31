---
title: Using ASP to retrieve a recordset and return value from a stored procedure at the same time
author: kaht
type: post
date: 2009-08-10T18:43:33+00:00
excerpt: |
  (This post will use ASP, coded with server side JScript)
  
  When accessing an SQL stored procedure in ASP via ADO, it is typically considered best practice to use the command object:
  
  
  var oConn = Server.CreateObject("ADODB.Connection");
  oConn.Open(&hellip;
url: /index.php/webdev/serverprogramming/classicasp/using-asp-to-retrieve-a-recordset-and-re/
views:
  - 33850
rp4wp_auto_linked:
  - 1
categories:
  - Classic ASP

---
_(This post will use ASP, coded with server side JScript)_

When accessing an SQL stored procedure in ASP via ADO, it is typically considered best practice to use the command object:

<pre>var oConn = Server.CreateObject("ADODB.Connection");
oConn.Open("Provider=SQLOLEDB; Data Source=ServerName; User Id=ID; Password=PASSWORD");
var oCmd = Server.CreateObject("ADODB.Command");
oCmd.ActiveConnection = oConn;
oCmd.CommandType = adCmdStoredProc;
oCmd.CommandText = "StoredProcedureName";
oCmd.Execute();
oCmd.Close();
oConn.Close();</pre>

If the stored procedure returns a recordset, then the Execute() method of the command object will return a recordset object.

Consder the following stored procedure:

<pre>create procedure TestProcedure as
select 1 [testValue] union
select 2 [testValue] union
select 3 [testValue] </pre>

Using a command object set up to call this procedure, we can access the returned recordset like so:

<pre>var oConn = Server.CreateObject("ADODB.Connection");
oConn.Open("Provider=SQLOLEDB; Data Source=ServerName; User Id=ID; Password=PASSWORD");
var oCmd = Server.CreateObject("ADODB.Command");
oCmd.ActiveConnection = oConn;
oCmd.CommandType = adCmdStoredProc;
oCmd.CommandText = "TestProcedure";
var oRS = oCmd.Execute();  //oRS will contain the recordset returned from the stored procedure
while (!oRS.EOF) {
   Response.Write(oRS.Fields("testValue").value + "&lt;br /&gt;");
   oRS.MoveNext();
}
oRS.Close();
oCmd.Close();
oConn.Close();</pre>

If a stored procedure returns a return value, then a return value parameter is required on the ADO command object.

Consider the following procedure:

<pre>create procedure TestProcedure2 as
return 1</pre>

To retrieve the return value in the stored procedure, you have to set up a parameter on the command object to store the return value:

<pre>var oConn = Server.CreateObject("ADODB.Connection");
oConn.Open("Provider=SQLOLEDB; Data Source=ServerName; User Id=ID; Password=PASSWORD");
var oCmd = Server.CreateObject("ADODB.Command");
oCmd.ActiveConnection = oConn;
oCmd.CommandType = adCmdStoredProc;
oCmd.CommandText = "TestProcedure2";
oCmd.Parameters.Append(oCmd.CreateParameter("@returnValue", adInteger, adParamReturnValue));
oCmd.Execute();
Response.Write("return value: " + oCmd.Parameters("@returnValue").value);
oCmd.Close();
oConn.Close();</pre>

Now, things get a little tricky if you have a recordset and a return value in the same procedure:

<pre>create procedure TestProcedure3 as
select 1 [testValue] union
select 2 [testValue] union
select 3 [testValue] 
return 1</pre>

The recordset object will be populated correctly, but the return value parameter returns &#8220;undefined&#8221;:

<pre>var oConn = Server.CreateObject("ADODB.Connection");
oConn.Open("Provider=SQLOLEDB; Data Source=ServerName; User Id=ID; Password=PASSWORD");
var oCmd = Server.CreateObject("ADODB.Command");
oCmd.ActiveConnection = oConn;
oCmd.CommandType = adCmdStoredProc;
oCmd.CommandText = "TestProcedure2";
oCmd.Parameters.Append(oCmd.CreateParameter("@returnValue", adInteger, adParamReturnValue));
var oRS = oCmd.Execute();
Response.Write("return value: " + oCmd.Parameters("@returnValue").value);
oCmd.Close();
oConn.Close();</pre>

One thing that may seem even more odd is that if you do not assign the recordset returned by the procedure to a variable (oRS in the example above), then the returnValue parameter contains the correct value.

The reason for this is that the recordset returned by the stored procedure must be closed before you can access the returnValue parameter. I find using the GetRows() method to be extremely handy for this as it allows you to pull all the recordset information into an ASP array and close the recordset immediately afterward, giving you access to both the recordset information and the return value at the same time.

<pre>var oConn = Server.CreateObject("ADODB.Connection");
oConn.Open("Provider=SQLOLEDB; Data Source=ServerName; User Id=ID; Password=PASSWORD");
var oCmd = Server.CreateObject("ADODB.Command");
oCmd.ActiveConnection = oConn;
oCmd.CommandType = adCmdStoredProc;
oCmd.CommandText = "TestProcedure2";
oCmd.Parameters.Append(oCmd.CreateParameter("@returnValue", adInteger, adParamReturnValue));
var oRS = oCmd.Execute();
if (oRS.RecordCount) {
   var recordSetData = oRS.GetRows();
}
oRS.Close()
Response.Write("return value: " + oCmd.Parameters("@returnValue").value);
oCmd.Close();
oConn.Close();</pre>