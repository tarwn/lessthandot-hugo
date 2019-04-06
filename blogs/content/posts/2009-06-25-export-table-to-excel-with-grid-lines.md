---
title: Export HTML table to Excel with grid lines
author: kaht
type: post
date: 2009-06-25T17:47:45+00:00
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

<pre>Response.ContentType = "application/ms-excel";
Response.AddHeader("content-disposition", "attachment; filename=test.xls");</pre>

However, when the excel file is generated it has a nasty side effect of having all the gridlines hidden. They can easily be turned back on in excel by the following: Tools > Options > click gridlines checkbox.

Until today I put up with the gridlines being hidden. When I tried to search for a solution via google, most people suggested that it just wasn&#8217;t possible to generate the excel report with gridlines. Other people offered solutions that required you to run a COM object on the server to start an instance of excel in the background to create the file. However, after searching through a bunch of garbage and piecing together bits and pieces of non-working solutions, I finally got it to work. The trick is to set up your own custom XML settings, and add the &#8220;Panes&#8221; worksheet option. Here was the working solution:

<pre>using System;
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
         Response.Write("&lt;html xmlns:x="urn:schemas-microsoft-com:office:excel"&gt;");
         Response.Write("&lt;head&gt;");
         Response.Write("&lt;meta http-equiv="Content-Type" content="text/html;charset=windows-1252"&gt;");
         Response.Write("&lt;!--[if gte mso 9]&gt;");
         Response.Write("&lt;xml&gt;");
         Response.Write("&lt;x:ExcelWorkbook&gt;");
         Response.Write("&lt;x:ExcelWorksheets&gt;");
         Response.Write("&lt;x:ExcelWorksheet&gt;");
         //this line names the worksheet
         Response.Write("&lt;x:Name&gt;gridlineTest&lt;/x:Name&gt;");
         Response.Write("&lt;x:WorksheetOptions&gt;");
         //these 2 lines are what works the magic
         Response.Write("&lt;x:Panes&gt;");
         Response.Write("&lt;/x:Panes&gt;");
         Response.Write("&lt;/x:WorksheetOptions&gt;");
         Response.Write("&lt;/x:ExcelWorksheet&gt;");
         Response.Write("&lt;/x:ExcelWorksheets&gt;");
         Response.Write("&lt;/x:ExcelWorkbook&gt;");
         Response.Write("&lt;/xml&gt;");
         Response.Write("&lt;![endif]--&gt;");
         Response.Write("&lt;/head&gt;");
         Response.Write("&lt;body&gt;");
         Response.Write("&lt;table&gt;");
         Response.Write("&lt;tr&gt;&lt;td&gt;ID&lt;/td&gt;&lt;td&gt;Name&lt;/td&gt;&lt;td&gt;Balance&lt;/td&gt;&lt;/tr&gt;");
         Response.Write("&lt;tr&gt;&lt;td&gt;1234&lt;/td&gt;&lt;td&gt;Al Bundy&lt;/td&gt;&lt;td&gt;45&lt;/td&gt;&lt;/tr&gt;");
         Response.Write("&lt;tr&gt;&lt;td&gt;9876&lt;/td&gt;&lt;td&gt;Homer Simpson&lt;/td&gt;&lt;td&gt;-129&lt;/td&gt;&lt;/tr&gt;");
         Response.Write("&lt;tr&gt;&lt;td&gt;5555&lt;/td&gt;&lt;td&gt;Peter Griffin&lt;/td&gt;&lt;td&gt;0&lt;/td&gt;&lt;/tr&gt;");
         Response.Write("&lt;/table&gt;");
         Response.Write("&lt;/body&gt;");
         Response.Write("&lt;/html&gt;");
      }
   }
}</pre>

Got a web related question? Discuss it in the forums: http://forum.lessthandot.com/