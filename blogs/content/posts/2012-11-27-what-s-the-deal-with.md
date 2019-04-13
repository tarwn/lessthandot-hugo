---
title: 'What’s the deal with Excel & SSIS?'
author: Koen Verbeeck
type: post
date: 2012-11-27T10:45:00+00:00
ID: 1803
excerpt: 'When it comes to importing data from an Excel sheet with SSIS, Excel has quite a reputation. And not a terribly good one. Well deserved, to be honest, because numerous issues can rise when dealing with this piece of software. This blog post will deal wi&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/what-s-the-deal-with/
views:
  - 86994
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - SSIS
tags:
  - ace ole db
  - excel
  - imex=1
  - jet ole db
  - ssis
  - syndicated
  - typeguessrows

---
<p style="text-align: justify;">
  When it comes to importing data from an Excel sheet with SSIS, Excel has quite a reputation. And not a terribly good one. Well deserved, to be honest, because numerous issues can rise when dealing with this piece of software. This blog post will deal with the issue I encounter the most on forums, which is the issue of the mixed data types in one column, also known as <em>“Why is some of my data read as NULL?”</em>.
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">There are already a numerous blog posts, articles and forum posts written on this subject, but most of them only deal with parts of the issue. I would like a single, comprehensive reference I can point to in the forums, hence this blog post. And because, you know, world domination has to start somewhere…</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <img style="text-align: left;" src="/wp-content/uploads/users/koenverbeeck/DealBetweenExcelSSIS/ugly_duckling.jpg?mtime=1353999051" alt="Excel: the ugly duckling of source files" width="250" height="173" />
</p>

<p style="text-align: left;">
  <em>Excel: the ugly duckling of all import files (<a href="http://www.flickr.com/photos/nickymccrea/7321735702/" target="_blank">source</a>)</em>
</p>

<span style="font-weight: bold; text-align: justify;">The issue</span>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">You have an Excel sheet with data that you would like to import with SSIS. One of the columns has both text data as numeric data. Take this sample as an example:</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <a href="/media/users/koenverbeeck/DealBetweenExcelSSIS/ExcelSample.png?mtime=1353999037"><img src="/wp-content/uploads/users/koenverbeeck/DealBetweenExcelSSIS/ExcelSample.png?mtime=1353999037" alt="" width="673" height="256" /></a>
</p>

<div class="image_block" style="text-align: justify;">
  The postal code in Belgium usually consists of 4 digits. However, sometimes it is prefixed with “<em>B-</em>“, which makes the data alphanumeric. The house number column has a value containing the word “bus”, so it becomes alphanumeric as well. When we pull this data into the SSIS dataflow, we get the following:
</div>

<div class="image_block">
  <a href="/media/users/koenverbeeck/DealBetweenExcelSSIS/Dataviewer_1.png?mtime=1353998990"><img style="float: left;" src="/wp-content/uploads/users/koenverbeeck/DealBetweenExcelSSIS/Dataviewer_1.png?mtime=1353998990" alt="" width="618" height="260" /></a>
</div>

 

 

 

 

 

 

 

 

 

<span style="text-align: justify;">All the alphanumeric data is read as NULL, while the numeric values come in untouched. The problem is not with SSIS, but with the JET OLE DB provider – used to read Excel 2003 or older – or with the ACE OLE DB provider, which is used to read Excel 2007 or newer. Both providers sample the data in a column to determine the data type. The data type with the most occurrences in the sample wins and is selected as the source data type in SSIS. In our sample, the resulting data type is numeric. Since the alphanumeric postal codes cannot be converted to a numeric value, those values are replaced with NULL. Not exactly what we want when extracting data for our ETL process.</span>

<p class="MsoNormal">
  <strong><span lang="EN-US">The solution</span></strong>
</p>

<p class="MsoNormal">
  <span lang="EN-US">The answer to the issue is to add <em>IMEX=1</em> to the extended properties of the connection string. This tells the provider if intermixed data types are found, the data type specified in the <em>ImportMixedTypes</em> registry setting is taken, which has the default of <em>text</em>. (You can find a list of all the registry paths at the bottom of this blog post). A typical connection string would look like this:</span>
</p>

<p class="MsoNormal" style="margin-left: 35.4pt; text-align: left;">
  <strong><em><span lang="EN-US">Provider=Microsoft.Jet.OLEDB.4.0; Data Source=C:MyExcel.xls;Extended Properties=”Excel 8.0;HDR=Yes;IMEX=1″;</span></em></strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">You can find more about connection strings at </span><a href="http://www.connectionstrings.com/" target="_blank"><span lang="EN-US">www.connectionstrings.com</span></a><span lang="EN-US">. In SSIS, you can only modify the Excel connection string manually in the properties window of the Excel connection manager. It cannot be changed through the GUI by double clicking the connection manager. It is possible that you get a warning at the source component, telling you the metadata of some columns have changed. This is normal, as our offending columns have now changed to a string data type.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">After adding IMEX=1, we get this result:</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <a href="/media/users/koenverbeeck/DealBetweenExcelSSIS/Dataviewer_2.png?mtime=1353998997"><img src="/wp-content/uploads/users/koenverbeeck/DealBetweenExcelSSIS/Dataviewer_2.png?mtime=1353998997" alt="" width="612" height="278" /></a>
</p>

<span style="text-align: justify;">That looks a little bit better, doesn’t it?</span>

<p class="MsoNormal" style="text-align: justify;">
  <strong><span lang="EN-US">However…</span></strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">The NULL in the house number column disappeared and made place for the actual value, but the NULL in the zip code column is still present. What did just happen here? Remember when I said earlier that the providers <em>sample</em> the data to determine the data type? The JET and ACE OLE DB provider take the first x number of rows, where x is the number specified in the <em>TypeGuessRows</em> registry setting, which has the default of 8. If the first occurrence of intermixed data types happens after this sample, the providers take the data type found in the sample. Again, big trouble for us as we’re still losing data.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <strong><span lang="EN-US">The solution part II</span></strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">Luckily the solution is fairly simple: set either the <em>TypeGuessRows</em> registry setting to a higher number (a maximum of 16) or set it to 0. A very popular myth is that setting it to 0 causes the providers to scan all data. However, this is not true as indicated by this <a href="http://support.microsoft.com/kb/281517" target="_blank">KB article</a>. Only the first 16384 rows are scanned, which is in my opinion a pretty high number. Be aware that a performance hit might be possible with larger Excel workbooks as the provider needs to scan more data.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">Make sure you change the registry property in the right version of the providers. The ACE OLE DB provider used in Office 2010 is version 14.0, but the SSIS connection string mentions version 12.0. A big thanks to Valentino (<a href="http://blog.hoegaerden.be/" target="_blank">blog</a> | <a href="https://twitter.com/ValentinoV42" target="_blank">twitter</a>) who pointed me at this problem. You can find more information in <a href="http://blog.hoegaerden.be/2010/03/29/retrieving-data-from-excel/" target="_blank">his article</a> (look at the bottom for the important update).</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">After updating the registry we finally get all our values:</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <a href="/media/users/koenverbeeck/DealBetweenExcelSSIS/Dataviewer_3.png?mtime=1353999004"><img style="float: left;" src="/wp-content/uploads/users/koenverbeeck/DealBetweenExcelSSIS/Dataviewer_3.png?mtime=1353999004" alt="" width="606" height="251" /></a>
</p>

 

 

 

 

 

 

 

 

<span style="font-weight: bold; text-align: justify;">But what if…</span>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">What if the first occurrence of intermixed data types happens after row number 16384? (You just don’t have any luck, don’t you?) Or what if the administrator doesn’t allow you to modify the registry? (Always the same with those paranoia people) If you can control the template of the Excel, you can do the following: add a dummy row at the beginning of the sheet, which has alphanumeric data for the violating columns. This way you’ll be sure string data is always included in the sample and that the resulting data type is always alphanumeric. Hide the dummy row in the template and get rid of it in the SSIS dataflow using a conditional split.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">Another alternative you could try is to switch the cell formatting from General to Text, if possible.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">But what if you can’t control the template? Sometimes the Excel files are automatically generated or supplied by a third party. Suck it up and try convincing the supplier of the Excel files to use a file type that is actually intended to store flat file data, such as .csv files or ragged right flat files, instead of bug-inducing spreadsheets. Believe me; it will make your life easier.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <strong><span lang="EN-US">The truncated text problem</span></strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">A very similar issue is when you have a text column in your Excel sheets that sometimes has more than 255 characters in a single cell. A common example is a “comments” column. When you try to import this, you can get this error in SSIS:</span>
</p>

<p class="MsoNormal" style="text-align: left;">
  <em><span lang="EN-US">[Excel Source [5]] Error: There was an error with Excel Source.Outputs[Excel Source Output].Columns[Comments] on Excel Source.Outputs[Excel Source Output]. The column status returned was: “Text was truncated or one or more characters had no match in the target code page.”.</span></em>
</p>

<p class="MsoNormal">
  <a href="/media/users/koenverbeeck/DealBetweenExcelSSIS/ExcelComments.png?mtime=1353999029"><img src="/wp-content/uploads/users/koenverbeeck/DealBetweenExcelSSIS/ExcelComments.png?mtime=1353999029" alt="" width="332" height="334" /></a>
</p>

_Bob really hated that customer service…_

<span style="font-weight: bold;">The solution part III</span>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">The problem is the same as before: the provider scans the first 8 rows and finds only text of normal length. SSIS takes the default data type of (DT_WSTR,255) for all string data. Our comments are sometimes larger than 255 characters, causing the truncation error.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">If your comments have a maximum length lower than the 4000 character limit of DT_WSTR, you can simply adjust the length of the column in the advanced editor of the source component or you can configure the source to ignore the failure. The last option does give you truncated data though. This <a href="http://www.bradleyschacht.com/excel-source-the-output-column-failed-becuase-truncation-occured/" target="_blank">blog post</a> by  Bradley Schacht (<a href="http://www.bradleyschacht.com/" target="_blank">blog</a> | <a href="https://twitter.com/bradleyschacht" target="_blank">twitter</a>) describes how you can make those changes.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">If the length exceeds 4000 characters, we have a totally different problem, since it doesn’t fit into the DT_WSTR data type. We need the DT_NTEXT data type – which is a LOB data type – to store the data into the pipeline. This corresponds with a text or nvarchar(max) data type in SQL Server. The solution is the same as before: adjust the TypeGuessRows registry setting so the provider will pick up those lengthy comments and determine the correct data type to store the values.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <a href="/media/users/koenverbeeck/DealBetweenExcelSSIS/Dataviewer_4.png?mtime=1353999022"><img src="/wp-content/uploads/users/koenverbeeck/DealBetweenExcelSSIS/Dataviewer_4.png?mtime=1353999022" alt="" width="400" height="239" /></a>
</p>

_The values are shown as <Long Text> as the data viewer can't show LOB data._

<span style="text-align: justify;">Sometimes SSIS and Excel stubbornly refuse to play nice together and won't adjust the column data type. In that case you’ll have to adjust the data type yourself in the advanced editor of the Excel source.</span>

<p class="MsoNormal" style="text-align: justify;">
  <strong><span lang="EN-US">Conclusion</span></strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">This blog post deals with one of the most common issue found when working with SSIS and Excel: the determination of the source data types by the JET and ACE OLE DB providers. Most issues can easily be solved by adjusting some registry settings. Follow these easy steps to import your Excel data with the least amount of issues:</span>
</p>

  1. <span style="text-indent: -18pt;" lang="EN-US">Add IMEX=1 to the connection string.</span>
  2. <span style="text-indent: -18pt;" lang="EN-US">Set the TypeGuessRows registry setting to 0.</span>
  3. <span style="text-indent: -18pt;" lang="EN-US">Double check the data type in the advanced editor of the source component.</span>

<!--[if !supportLists]-->

<p class="MsoNormal">
  <strong><span lang="EN-US">Addendum: the registry settings</span></strong>
</p>

<table class="MsoTableMediumShading1Accent1" style="width: 730px; border-collapse: collapse; border: none;" border="1" cellspacing="0" cellpadding="0">
  <tr>
    <td style="width: 66.05pt; border: solid #7BA0CD 1.0pt; mso-border-themecolor: accent1; mso-border-themetint: 191; border-right: none; background: #4F81BD; mso-background-themecolor: accent1; padding: 0cm 5.4pt 0cm 5.4pt;" width="88" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 5;">
        <strong><span style="color: white; mso-themecolor: background1; mso-ansi-language: EN-US;" lang="EN-US">Provider</span></strong>
      </p>
    </td>
    
    <td style="width: 95.3pt; border-top: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; border-left: none; border-bottom: solid #7BA0CD 1.0pt; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; border-right: none; background: #4F81BD; mso-background-themecolor: accent1; padding: 0cm 5.4pt 0cm 5.4pt;" width="127" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 1;">
        <span style="color: white; mso-themecolor: background1; mso-ansi-language: EN-US; mso-bidi-font-weight: bold;" lang="EN-US">Values</span>
      </p>
    </td>
    
    <td style="width: 318.95pt; border: solid #7BA0CD 1.0pt; mso-border-themecolor: accent1; mso-border-themetint: 191; border-left: none; background: #4F81BD; mso-background-themecolor: accent1; padding: 0cm 5.4pt 0cm 5.4pt;" width="425" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 1;">
        <span style="color: white; mso-themecolor: background1; mso-ansi-language: EN-US; mso-bidi-font-weight: bold;" lang="EN-US">Path</span>
      </p>
    </td>
  </tr>
  
  <tr>
    <td style="width: 66.05pt; border-top: none; border-left: solid #7BA0CD 1.0pt; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; border-bottom: solid #7BA0CD 1.0pt; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; border-right: none; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; background: #D3DFEE; mso-background-themecolor: accent1; mso-background-themetint: 63; padding: 0cm 5.4pt 0cm 5.4pt;" width="88" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 68;">
        <span lang="EN-US">JET OLE DB</span>
      </p>
    </td>
    
    <td style="width: 95.3pt; border: none; border-bottom: solid #7BA0CD 1.0pt; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; background: #D3DFEE; mso-background-themecolor: accent1; mso-background-themetint: 63; padding: 0cm 5.4pt 0cm 5.4pt;" width="127" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 64;">
        <span lang="EN-US">TypeGuessRows<br /> ImportMixedTypes</span>
      </p>
    </td>
    
    <td style="width: 318.95pt; border-top: none; border-left: none; border-bottom: solid #7BA0CD 1.0pt; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; border-right: solid #7BA0CD 1.0pt; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; background: #D3DFEE; mso-background-themecolor: accent1; mso-background-themetint: 63; padding: 0cm 5.4pt 0cm 5.4pt;" width="425" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 64;">
        <span lang="EN-US">HKEY_LOCAL_MACHINESoftwareMicrosoftJet4.0EnginesExcel</span>
      </p>
    </td>
  </tr>
  
  <tr>
    <td style="width: 66.05pt; border-top: none; border-left: solid #7BA0CD 1.0pt; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; border-bottom: solid #7BA0CD 1.0pt; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; border-right: none; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0cm 5.4pt 0cm 5.4pt;" width="88" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 132;">
        <span lang="EN-US">ACE OLE DB</span>
      </p>
    </td>
    
    <td style="width: 95.3pt; border: none; border-bottom: solid #7BA0CD 1.0pt; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0cm 5.4pt 0cm 5.4pt;" width="127" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 128;">
        <span lang="EN-US">TypeGuessRows<br /> ImportMixedTypes<strong> </strong></span>
      </p>
    </td>
    
    <td style="width: 318.95pt; border-top: none; border-left: none; border-bottom: solid #7BA0CD 1.0pt; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; border-right: solid #7BA0CD 1.0pt; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0cm 5.4pt 0cm 5.4pt;" width="425" valign="top">
      <p class="MsoNormal" style="margin-bottom: .0001pt; text-align: justify; line-height: normal; mso-yfti-cnfc: 128;">
        <span lang="EN-US">HKEY_LOCAL_MACHINESoftwareMicrosoftOffice12.0Access Connectivity EngineEnginesExcel <em>(*)</em><strong> </strong></span>
      </p>
    </td>
  </tr>
</table>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US"><em>(*) For newer versions of Office, you need to replace 12.0 with the correct version number. It took me only 1 hour of debugging to figure that one out.</em></span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">Note: in 64-bit systems, the values for the 32-bit providers should be located in the Wow6432Node.</span>
</p>