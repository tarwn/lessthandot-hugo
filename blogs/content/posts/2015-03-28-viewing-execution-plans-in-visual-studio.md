---
title: Viewing Execution Plans in Visual Studio
author: Jes Borland
type: post
date: 2015-03-28T19:55:17+00:00
ID: 3320
url: /index.php/uncategorized/viewing-execution-plans-in-visual-studio/
views:
  - 5225
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
SQL Server execution plans are invaluable for figuring out how a query was run &#8211; and how to make it run better. Most of the time that I spend with execution plans is in either SQL Server Management Studio or, more frequently, SQL Sentry's Plan Explorer. However, many developers spend most of their time in Visual Studio. Is there a way for you to view execution plans using Visual Studio? Yes!

I downloaded and installed <a href="https://www.visualstudio.com/en-us/products/compare-visual-studio-products-vs.aspx" target="_blank">Visual Studio Premium 2013</a> with SQL Server Data Tools. This is the first time I've used Visual Studio in…many years. I wasn't sure what had changed, how much I'd remember about it, and how easy it would be to figure out what I wanted. Turns out, it's pretty easy to do what I wanted: connect to a database, open a query, and see if I could view an execution plan.

To start, I selected File > New > Project.

On the left I expand Installed > Templates and select SQL Server. I name it and select OK.

[<img class="aligncenter size-full wp-image-3321" src="/wp-content/uploads/2015/03/VSNewProject.png" alt="VSNewProject" width="956" height="580" srcset="/wp-content/uploads/2015/03/VSNewProject.png 956w, /wp-content/uploads/2015/03/VSNewProject-300x182.png 300w" sizes="(max-width: 956px) 100vw, 956px" />][1]

&nbsp;

The first thing I want to do is connect to my database. I expand out the Server Explorer and click the Add SQL Server option.

[<img class="aligncenter size-full wp-image-3323" src="/wp-content/uploads/2015/03/VSServerExplorer1.png" alt="VSServerExplorer" width="304" height="225" srcset="/wp-content/uploads/2015/03/VSServerExplorer1.png 304w, /wp-content/uploads/2015/03/VSServerExplorer1-300x222.png 300w" sizes="(max-width: 304px) 100vw, 304px" />][2]

A connection box opens; I enter my SQL Server name and credentials. This verifies I have permission to view the server. I can now select View > SQL Server Object Explorer to view the server objects in that pane.

[<img class="aligncenter size-full wp-image-3324" src="/wp-content/uploads/2015/03/VSSQLServerObjectExplorer.png" alt="VSSQLServerObjectExplorer" width="306" height="324" srcset="/wp-content/uploads/2015/03/VSSQLServerObjectExplorer.png 306w, /wp-content/uploads/2015/03/VSSQLServerObjectExplorer-283x300.png 283w" sizes="(max-width: 306px) 100vw, 306px" />][3]To start a new query, I go to Tools > SQL Server > New Query. I can open an existing query by going to File > Open > File and selecting a .sql file. The query window has a toolbar that has some of the same options as SSMS, although the icons look different.

[<img class="aligncenter size-full wp-image-3326" src="/wp-content/uploads/2015/03/VSQueryWithToolbar.png" alt="VSQueryWithToolbar" width="636" height="145" srcset="/wp-content/uploads/2015/03/VSQueryWithToolbar.png 636w, /wp-content/uploads/2015/03/VSQueryWithToolbar-300x68.png 300w" sizes="(max-width: 636px) 100vw, 636px" />][4]

1. Display Estimated Execution Plan

2. Include Actual Execution Plan

3. SQLCMD mode

The estimated and actual execution plans look and behave the same as they do in SSMS.

<div id="attachment_3327" style="width: 1037px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/03/VSActualExecPlan.png"><img class="size-full wp-image-3327" src="/wp-content/uploads/2015/03/VSActualExecPlan.png" alt="An actual execution plan" width="1027" height="584" srcset="/wp-content/uploads/2015/03/VSActualExecPlan.png 1027w, /wp-content/uploads/2015/03/VSActualExecPlan-300x170.png 300w, /wp-content/uploads/2015/03/VSActualExecPlan-1024x582.png 1024w" sizes="(max-width: 1027px) 100vw, 1027px" /></a>
  
  <p class="wp-caption-text">
    An actual execution plan
  </p>
</div>

This ability can be helpful if you spend a lot of time working in Visual Studio and want to access execution plans.

 [1]: /wp-content/uploads/2015/03/VSNewProject.png
 [2]: /wp-content/uploads/2015/03/VSServerExplorer1.png
 [3]: /wp-content/uploads/2015/03/VSSQLServerObjectExplorer.png
 [4]: /wp-content/uploads/2015/03/VSQueryWithToolbar.png