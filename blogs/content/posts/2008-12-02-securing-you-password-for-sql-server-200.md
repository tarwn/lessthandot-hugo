---
title: Securing your password for SQL Server 2005 and 2008 and more
author: Ted Krueger (onpnt)
type: post
date: 2008-12-02T12:31:09+00:00
url: /index.php/datamgmt/datadesign/securing-you-password-for-sql-server-200/
views:
  - 26579
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin

---
Do you know your sa password? Or let me put that a different way and say, does anyone besides you know the sa password? To build on that question, does anyone know the AD SQL Server service account password? Or any service account passwords. How about some of the sql authenticated users you&#8217;ve setup for reporting, system integration etc&#8230; ????

If you just said yes and they are in an excel file sitting either in a network drive or on your machine then you should be flipping burgers at McDonalds. Yes I really do feel that way. What is the sense of us as systems security and support employees that are entrusted with these key strings that can gain access to just about anything the company has to hold personal. BOM&#8217;s, Sales, Customers and on. All of this will mean nothing if you don&#8217;t also secure the passwords to gain access to your instances.

Luckily there is an easy fix to this and although it being not 100% secure (which nothing is) and having one loop hole that I can think of off the top of my head, at least your excel file can&#8217;t be somehow opened by Bob Johnson the weekend hacker you hired last week as a customer service associate to answer hate calls. Just think how Bob is going to feel about his job after months of listening to people complain about his company. Yes when the customer calls usually it will be his company at that point to the customer.

Ok back to the easy fix. SQL Server 2005 and 2008 make it easy to setup certificates and keys allowing you to insert data into a table while encrypting it using SQL Server. 

Ok so you want to see some cool graphs? Click here http://msdn.microsoft.com/en-us/library/ms189586.aspx

Ok pictures aside in order to get yourself in the position to encrypt data you first need a cert. The certificate will give you the ability to then create a key in order to encrypt the data when inserting into the tables you have. Encryption can be intimidating which is why I thought of writing this. I want to write by example and not give you a bunch of techy garbage that will only confuse you. I think the power of learning is by means of the ability to perform. So back on track we&#8217;re going to create a simplistic ASP.NET front-end for you to view and add network and SQL Server authenticated passwords using a self-signed certificate and symmetric key.

Let’s do it&#8230;

First let&#8217;s get your database setup. Notes: encryption is slow. Don’t forget that! Also don&#8217;t fall under the belief the master key can encrypt data. You still need to create a key.

Again I use a DBA database on all instances so you will see that through my blogs. This code is littered all over the internet. In fact a friend and probably the best DBA I know has a much better blog on just this @ http://searchsqlserver.techtarget.com/tip/0,289483,sid87_gci1285699,00.html# I suggest you read it. Denny goes into far better encryption and algorithm details than I.

Here are the steps in order

1. Create a table to hold your passwords and relevant data

2. Create your certificate to gain access to our key. Don’t forget to put a description on it so you know what it&#8217;s for or the next poor DBA does. My example does not use a string password but I highly recommend more complexity to it. 

3. Create you symmetric key passed of your new certificate and we&#8217;ll use the AES_256 algorithm. (note: windows 2000 and or Xp does not support this. You’ll have to choose another encryption. Read here http://msdn.microsoft.com/en-us/library/ms345262.aspx)

4. This is the step that is what you will utilize on insert every time. Open the key. 

5. Create a variable to hold guid of the key.

6. Using the EncryptByKey function to do the work insert your data

7. You can select on it to see by using the DecryptByKey function and then ALWAYS close your key

<pre>CREATE TABLE SSPasswordList (EncrypPwdPhrase VARBINARY(255),UserN Varchar(255), SystemN Varchar(255), SystemType varchar(3))
Go
CREATE CERTIFICATE PasswordCert 
ENCRYPTION BY PASSWORD = 'mycert.1'
WITH SUBJECT = 'Password Cert', 
EXPIRY_DATE = '01/01/2099';
GO

CREATE SYMMETRIC KEY PasswordKey WITH ALGORITHM = AES_256
ENCRYPTION BY CERTIFICATE PasswordCert;
GO

OPEN SYMMETRIC KEY PasswordKey DECRYPTION BY CERTIFICATE PasswordCert WITH PASSWORD ='mycert.1';

DECLARE @GUID UNIQUEIDENTIFIER

SELECT @GUID = KEY_GUID FROM sys.symmetric_keys WHERE Name = 'PasswordKey'
INSERT INTO SSPasswordList (EncrypPwdPhrase,UserN,SystemN,SystemType) VALUES (EncryptByKey(@Guid, '123456', 1),'sa','My instance sa password','SQL')

SELECT CONVERT(VARCHAR(20), DecryptByKey(SSPasswordList.EncrypPwdPhrase, 1)) AS Data,* FROM SSPasswordList

CLOSE SYMMETRIC KEY PasswordKey
Go</pre>

Let&#8217;s make this easy for us to use now. create these two proc&#8217;s in your DBA database

<pre>Create Proc [dbo].[GrabPassword] (@userN varchar(610))
As
SET NOCOUNT ON
OPEN SYMMETRIC KEY PasswordKey DECRYPTION BY CERTIFICATE PasswordCert WITH PASSWORD ='mycert.1';

SELECT CONVERT(VARCHAR(20), DecryptByKey(SSPasswordList.EncrypPwdPhrase, 1)) as pwd FROM SSPasswordList Where 'User: ' + UserN + ' On: ' + SystemN = @userN

CLOSE SYMMETRIC KEY PasswordKey
SET NOCOUNT OFF
Go


Create proc [dbo].[GrabNameList](@type varchar(255))
as 
select 'User: ' + UserN + ' On: ' + SystemN from SSPasswordList where SystemType = @type</pre>

So how can we use this then. Let&#8217;s do this. I&#8217;m not going to talk about how to write ASP.NET or C# or anything on IIS etc&#8230; There are plenty of other things out there to help you with that. I&#8217;m just giving you the code so you can try it out. Create a new web site in VS.NET (I&#8217;m on 2.0 and 2005). In your Default.aspx replace the code with the below

<pre><%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>
<% @ Import Namespace="System.Data.SqlClient" %>
<% @ Import Namespace="System.Data" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" >
<head id="Head1" runat="server">
<title>Password Manager</title>
<style type="text/css">
Body {
background-color: #666666;
}

.button {
 background: #CCCCCC;
 border: 1px solid #97AAC7;
 font-family: Verdana, Helvetica, Sans-Serif;
 font-size: 11px;
 margin-top: 2px;
 width:150px;
}

table {
 background-color: #E4EAF2;
 text-align:center;
 width:70%;
 height:50%;
 border: 1px solid #000000;
}
TD {

}
.wrap {
 width:100%;
 height:100%;
 text-align:center;
 margin-top:100px;
}
.mid {
 width:90%;
 height:400px;
}
</style>
<script language="javascript" type="text/javascript">

function onSelType()
{
 var obj = document.getElementById('types');
 if(obj.options[obj.selectedIndex].value > 0)
 {
  if(window.location.href.indexOf("?") == -1)
  {
  window.location.href=window.location.href + "?types=" + obj.options[obj.selectedIndex].value;
  }
  else
  {
  window.location.href=window.location.href.substring(0,window.location.href.indexOf("?")) + "?types=" + obj.options[obj.selectedIndex].value;   
  }
 }
}
function onSelUser()
{
 var obj = document.getElementById('accounts');
var objTypes = document.getElementById('types');
if(window.location.href.indexOf("?") != -1)
{
window.location.href=window.location.href.substring(0,window.location.href.indexOf("?")) + "?userStr=" + obj.options[obj.selectedIndex].value + "&types=" + objTypes.options[objTypes.selectedIndex].value;   
}
}
function selList()
{
var sel = <%
if(Convert.ToString(Request.QueryString["types"]) == null)
{
Response.Write("0");
} else { 
Response.Write(Convert.ToString(Request.QueryString["types"]));
} %>;

if(sel > 0)
{
var selObj = document.getElementById("types");
selObj.options[sel].selected = true;
}
}
</script>
</head>
<body>
<div class="wrap">
<div class="mid">
 <form method="post" action="Default.aspx" id="frm" runat="server">
  <input type="hidden" value="" name="status" />
  <table cellpadding="10" cellspacing="0">
  <tr>
  <td colspan="2" style="font-weight:bolder;font-size:xx-large;">
<asp:Label ID="password" runat="server" Text=""></asp:Label>
</td>
  </tr>
  <tr>
  <td style="text-align:left;">
  Select Account Type:<br />
  </td>
  <td style="text-align:left;">
  <select id="types" name="types" onchange="onSelType();">
   <option>-- select password type --</option>
   <option value="1">SQL Server Passwords</option>
   <option value="2">Network Passwords</option>
  </select>
  </td>
  </tr>
  <tr>
  <td style="text-align:left;"> 
  Select Account:<br />
  </td>
  <td style="text-align:left;">
  <select name="accounts" onchange="onSelUser();">
   
  <%
SqlConnection conn = new SqlConnection("Data Source={instance};Initial Catalog=DBA;User Id=userid;Password=userpwd;");
SqlCommand cmd = new SqlCommand();
SqlDataAdapter adt = new SqlDataAdapter(cmd);
DataTable dt = new DataTable();
  int i = 0;
  if(Convert.ToString(Request.QueryString["userStr"]) == null)
  {
  Response.Write("<option>-- accounts available --</option>");
  }
  else
  {
  Response.Write("<option>" + Convert.ToString(Request.QueryString["userStr"]) + "</option>");
  }
string types = Convert.ToString(Request.QueryString["types"]);
string userStr = Convert.ToString(Request.QueryString["userStr"]);
if (types != null && types != "" && userStr == null)
{
if (Convert.ToInt32(types) > 0)
{
switch (types)
{
case "0":
break;
case "1":
if (User.IsInRole(@"ActiveDirectoryDBA Group"))
{
//grab accounts
cmd.Connection = conn;
cmd.CommandText = "Exec GrabNameList 'SQL'";
adt.Fill(dt);

while (i < dt.Rows.Count)
{
Response.Write("<option value='" + dt.Rows[i].ItemArray[0].ToString().Trim()+"'>" + dt.Rows[i].ItemArray[0].ToString().Trim() + "</option>");
i++;
}
}
break;
case "2":
if (User.IsInRole(@"ActiveDirectoryDomain Admins Group"))
{
//grab accounts
cmd.Connection = conn;
cmd.CommandText = "Exec GrabNameList 'ADM'";
adt.Fill(dt);

while (i < dt.Rows.Count)
{
Response.Write("<option value='"+ dt.Rows[i].ItemArray[0].ToString().Trim()+"'>" + dt.Rows[i].ItemArray[0].ToString().Trim() + "</option>");
i++;
}
}
break;
}
}
} 
  %>
  </select>
  </td>
  </tr>
  </table>
<asp:HiddenField ID="loader" runat="server" />
 </form>
</div>
</div>
<%
//Response.Write(loader.Value); 
%>
<script type="text/javascript">selList();</script>
</body>
</html></pre>

Code behind

<pre>using System;
using System.Data;
using System.Configuration;
using System.Web;
using System.Web.Security;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Web.UI.WebControls.WebParts;
using System.Web.UI.HtmlControls;
using System.Data.SqlClient;

public partial class _Default : System.Web.UI.Page
{
protected void Page_Load(object sender, EventArgs e)
{
Label password = (Label)(frm.FindControl("password"));

SqlConnection conn = new SqlConnection("Data Source={instance};Initial Catalog=DBA;User Id=userid;Password=userpwd;");
SqlCommand cmd = new SqlCommand();
SqlDataAdapter adt = new SqlDataAdapter(cmd);
DataTable dt = new DataTable();
string loader = Convert.ToString(Request.QueryString["userStr"]);

if (loader != null && loader != "")
{
cmd.Connection = conn;
cmd.CommandText = "Exec GrabPassword '" + loader.Replace("'", "''") + "'";
adt.Fill(dt);

if (dt.Rows.Count == 1)
{
password.Text = "Password: " + dt.Rows[0].ItemArray[0].ToString();
}
}
conn.Dispose();
cmd.Dispose();
adt.Dispose();
}
}</pre>

OK explanations. First I control mostly everything by means of active directory. You probably should as well. This code above will utilize two groups, your typical domain admins group and then a DBA group. All my DBA&#8217;s are in this group (me 😉 right now). If the person executing this is not in the DBA group then they cannot see SQL Server authenticated passwords (type SQL). If they are not in the domain admin group then they can&#8217;t see domain accounts (type ADM). In order to get this code to work you will need to either comment those out or create the groups. I would create the groups and use this correctly as it will be more secure that way to normal users finding the site and checking the passwords out.

Now go delete that excel file and secure your passwords!!!