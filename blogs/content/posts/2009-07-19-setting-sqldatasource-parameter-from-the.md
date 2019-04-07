---
title: Setting SQLDataSource parameter from the code-behind
author: Naomi Nosonovsky
type: post
date: 2009-07-19T20:23:00+00:00
ID: 515
excerpt: |
  One of the often asked questions on ASP.NET forum I visit is how to change SQLDataSource parameter. I know two common solutions to this problem, though I only tried one in my own web-pages.
  
  I've just learned about another interesting solution which i&hellip;
url: /index.php/webdev/webdesigngraphicsstyling/setting-sqldatasource-parameter-from-the/
views:
  - 51011
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Web Design, Graphics and Styling

---
One of the often asked questions on ASP.NET forum I visit is how to change SQLDataSource parameter. I know two common solutions to this problem, though I only tried one in my own web-pages.

I&#8217;ve just learned about another interesting solution which is presented in Peter Kellner&#8217;s [blog][1]. See also [another blog][2] discussing this approach.

The solution I&#8217;m familiar with and used is to set the parameter in Selecting (Inserting / Updating for Insert / Update) events of SQLDataSource. Also if we want to retrieve an Output parameter of stored procedure, for example, we can use the Inserted (Updated / Deleted) events of SQLDataSource.

This is a sample of retrieving an output parameter&#8217;s value in Inserted event of SQLDataSource

```c#
#region DataSource Inserted
    protected void DataSource_Inserted(object sender, SqlDataSourceStatusEventArgs e)
    {
        if (e.Command.Parameters["@NewPersonID"].Value != DBNull.Value)
        { this.NewPersonID = Convert.ToInt32(e.Command.Parameters["@NewPersonID"].Value); }
        else
        { this.NewPersonID = 0; }
    }
    #endregion
```
where SQLDataSource definition in ASPX page looks like

```ASP.NET
<asp:SqlDataSource runat="server" ID="PeopleNoneDataSource" ConnectionString="<%$ ConnectionStrings:FCCMSConnectionString %>"
                InsertCommand="PersonInsert" InsertCommandType="StoredProcedure" OnInserted="DataSource_Inserted">
                <InsertParameters>
                    <asp:Parameter Name="NewPersonID" Direction="Output" Type="Int32" />
                    <asp:ProfileParameter Name="SiteID" PropertyName="SiteID" Type="Int32" />
                    <asp:QueryStringParameter Name="PersonType" QueryStringField="ptype" Type="Char"
                        DefaultValue="N" />
                    <asp:Parameter Name="Title" />
                    <asp:Parameter Name="Occupation" />
                    <asp:Parameter Name="BusinessName" />
                    <asp:Parameter Name="BusAdd1" />
                    <asp:Parameter Name="BusAdd2" />
                    <asp:Parameter Name="BusCity" />
                    <asp:Parameter Name="BusState" />
                    <asp:Parameter Name="BusZip" />
                    <asp:Parameter Name="BusEMail" />
                    <asp:Parameter Name="Pager" />
                    <asp:Parameter Name="Fax" />
                    <asp:Parameter Name="BusinessPhone" />
                    <asp:Parameter Name="SpouseFN" />
                    <asp:Parameter Name="SpouseLN" />
                    <asp:Parameter Name="SpouseID" />
                    <asp:Parameter Name="Anniversary" />
                </InsertParameters>
            </asp:SqlDataSource>
```

Same code by limno for VB.NET (see [ASP.NET forum&#8217;s thread][3])

```vb.net
Protected Sub SqlDataSource1_Inserted(ByVal sender As Object, ByVal e As   System.Web.UI.WebControls.SqlDataSourceStatusEventArgs)
  Response.Write("Record Inserted: " + Server.HtmlEncode(e.Command.Parameters("@ContactID").Value.ToString()) + "<br/>")   
 End Sub  
```
Here is the sample I found of setting parameter in Inserting event

```c#
#region SQL DataSource Inserting - setting parameters

    protected void DataSource_Inserting(object sender, SqlDataSourceCommandEventArgs e)
    {
   
        e.Command.Parameters["@PersonType"].Value = ViewState["PersonType"];//this.PersonType ;
        e.Command.Parameters["@SiteID"].Value = ConfigurationManager.AppSettings["SiteID"]; 
    }
    #endregion
```
Alternative method of setting parameter&#8217;s value is to use DefaultValue of parameter, like this

```vb.net
Protected Sub FormView1_ItemUpdating(ByVal sender As Object, ByVal e As System.Web.UI.WebControls.FormViewUpdateEventArgs) Handles FormView1.ItemUpdating
		SqlDataSource2.UpdateParameters("DateCompleted").DefaultValue = Now()
	End Sub

```
The above solution was used by Don Freeman and shared with UniversalThread members.

Here is a link demonstrating usage of the first method

http://forums.asp.net/t/1395687.aspx

I would also like to give a link to a pure ADO.NET solution for retrieving Identity field value after insert (without using SQLDataSource on the page)
  
[Getting ID of the newly inserted record in SQL Server Database using ADO.Net][4] 

Important information can be found at [Using Parameters with Data Source Controls][5]

Hope this blog helps you with your problem!

You may also visit our ASP.NET forum at LTD here [ASP.NET Forum at LTD][6]

 [1]: http://peterkellner.net/2006/09/18/expressionbuilderidentity/
 [2]: http://weblogs.asp.net/infinitiesloop/archive/2006/08/09/The-CodeExpressionBuilder.aspx
 [3]: http://forums.asp.net/p/1455158/3332004.aspx#3332004
 [4]: http://www.aspsnippets.com/post/2009/05/27/Get-ID-of-the-newly-inserted-record-in-SQL-Server-using-ADONet.aspx
 [5]: http://msdn.microsoft.com/en-us/library/xt50s8kz.aspx
 [6]: http://forum.ltd.local/viewforum.php?f=27