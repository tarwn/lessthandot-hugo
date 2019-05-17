---
title: Export HTML table to Excel with grid lines
author: kaht
type: post
date: 2009-06-25T17:47:45+00:00
ID: 482
excerpt: 'A programmatic example of how to export an HTML file to excel while keeping grid lines visible.  This solution does not require the use of a COM object.'
url: /index.php/webdev/serverprogramming/aspnet/export-table-to-excel-with-grid-lines/
views:
  - 62347
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Classic ASP
tags:
  - asp
  - asp.net
  - excel

---
For a long time I have had to make web reports for our company that could be exported to excel. This is a fairly easy process. All you have to do is put your report in an HTML table and add the following 2 lines of code:

```csharp
Response.ContentType = "application/ms-excel";
Response.AddHeader("content-disposition", "attachment; filename=test.xls");
```

However, when the excel file is generated it has a nasty side effect of having all the gridlines hidden. They can easily be turned back on in excel by the following: Tools > Options > click gridlines checkbox.

Until today I put up with the gridlines being hidden. When I tried to search for a solution via google, most people suggested that it just wasn't possible to generate the excel report with gridlines. Other people offered solutions that required you to run a COM object on the server to start an instance of excel in the background to create the file. However, after searching through a bunch of garbage and piecing together bits and pieces of non-working solutions, I finally got it to work. The trick is to set up your own custom XML settings, and add the "Panes" worksheet option. Here was the working solution:

```csharp
using System;
using System.Data;
using System.Configuration;
using System.Collections;
using System.Web;
using System.Web.Security;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Web.UI.WebControls.WebParts;
using System.Web.UI.HtmlControls;
 
namespace excelGridlineTest
{
   public partial class _Default : System.Web.UI.Page
   {
      protected void Page_Load(object sender, EventArgs e)
      {
         Response.ContentType = "application/ms-excel";
         Response.AddHeader("content-disposition", "attachment; filename=test.xls");
         Response.Write("<html xmlns:x="urn:schemas-microsoft-com:office:excel">");
         Response.Write("<head>");
         Response.Write("<meta http-equiv="Content-Type" content="text/html;charset=windows-1252">");
         Response.Write("<!--[if gte mso 9]>");
         Response.Write("<xml>");
         Response.Write("<x:ExcelWorkbook>");
         Response.Write("<x:ExcelWorksheets>");
         Response.Write("<x:ExcelWorksheet>");
         //this line names the worksheet
         Response.Write("<x:Name>gridlineTest</x:Name>");
         Response.Write("<x:WorksheetOptions>");
         //these 2 lines are what works the magic
         Response.Write("<x:Panes>");
         Response.Write("</x:Panes>");
         Response.Write("</x:WorksheetOptions>");
         Response.Write("</x:ExcelWorksheet>");
         Response.Write("</x:ExcelWorksheets>");
         Response.Write("</x:ExcelWorkbook>");
         Response.Write("</xml>");
         Response.Write("<![endif]-->");
         Response.Write("</head>");
         Response.Write("<body>");
         Response.Write("<table>");
         Response.Write("<tr><td>ID</td><td>Name</td><td>Balance</td></tr>");
         Response.Write("<tr><td>1234</td><td>Al Bundy</td><td>45</td></tr>");
         Response.Write("<tr><td>9876</td><td>Homer Simpson</td><td>-129</td></tr>");
         Response.Write("<tr><td>5555</td><td>Peter Griffin</td><td>0</td></tr>");
         Response.Write("</table>");
         Response.Write("</body>");
         Response.Write("</html>");
      }
   }
}
```

Got a web related question? Discuss it in the forums: http://forum.lessthandot.com/