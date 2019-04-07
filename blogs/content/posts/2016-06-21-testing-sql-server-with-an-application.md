---
title: Testing SQL Server with an Application
author: Jes Borland
type: post
date: 2016-06-21T14:12:38+00:00
ID: 4555
url: /index.php/datamgmt/dbprogramming/testing-sql-server-with-an-application/
views:
  - 6772
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
I’ve been blogging about SQL Server for years. Most of these blogs are designed for the database administrator or developer, and thus use SQL Server Management Studio exclusively. (Back in the day, I’d show SSRS a lot, too.)

But one of the questions that’s always been at the back of my mind is, “What does this look like from an application perspective?” I’ve been asking this question even more lately, as I work with Azure SQL Database and SQL Server 2016 features like Data Masking and Row Level Security. I’m used to having high-level permissions and executing queries in SSMS; what does it look like to a user that doesn’t have those permissions or tools?

I decided to dust off the programmings skills from that AAS – Programmer/Analyst degree I earned a few years ago. I’ve used Visual Studio to create a web application, connect that to my database, and show what would appear to a user. I don’t know if I did it the easy way, or the best way, and it’s definitely not a way that I would use for any real-world application. But it helps me get a sense of what it takes to implement a feature beyond the database.

I thought it might be helpful for others to see this process. Especially since you can now get all the tools to do this – within the Microsoft ecosystem – for free!

# Step 1: SQL Server 2016

Download SQL Server 2016 Developer Edition from <https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/sql-server-developer.aspx>. This version has all of the features of Enterprise Edition, but is free. No, you can’t use it in production. But it’s free!

After downloading, install and configure it. (Yes, even in my development environments, I configure things like max memory and cost threshold for parallelism and tempdb and trace flags.)

Now you need a database. AdventureWorks is an old standby, but there is a new test database available, WideWorldImporters! And it’s on GitHub! This is exciting. Download from <https://github.com/Microsoft/sql-server-samples/releases/tag/wide-world-importers-v1.0>. Restore the database into your SQL Server 2016 instance.

Then you need proper security for your database. Don’t run all demos as sa or as your own sysadmin-level account. That isn’t the real world, and not how users will access an application (or, it shouldn&#8217;t be). Add a Windows user if you can; I had to use SQL authentication for mine. I created a login named PurchaseOrdersApp. At the server level, permissions are public. At the database level, I granted db\_datareader and db\_datawriter.

<div id='gallery-2' class='gallery galleryid-4555 gallery-columns-2 gallery-size-thumbnail'>
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/Login-Properties.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/Login-Properties-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Username and password" aria-describedby="gallery-2-4558" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-2-4558'>
      Username and password
    </dd>
  </dl>
  
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/Login-Server-Roles.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/Login-Server-Roles-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Server role" aria-describedby="gallery-2-4559" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-2-4559'>
      Server role
    </dd>
  </dl>
  
  <br style="clear: both" />
  
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/Login-User-Mapping.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/Login-User-Mapping-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Database role" aria-describedby="gallery-2-4560" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-2-4560'>
      Database role
    </dd>
  </dl>
  
  <br style='clear: both' />
</div>

# Step 2: Visual Studio

Visual Studio Community 2015 is a free version that can be downloaded from <https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx>. Install and open, and you’ll see the Start Page.

[<img class="aligncenter wp-image-4565 size-medium" src="/wp-content/uploads/2016/06/Visual-Studio-Start-Page-300x163.png" alt="Visual Studio Start Page" width="300" height="163" srcset="/wp-content/uploads/2016/06/Visual-Studio-Start-Page-300x163.png 300w, /wp-content/uploads/2016/06/Visual-Studio-Start-Page-1024x557.png 1024w" sizes="(max-width: 300px) 100vw, 300px" />][1]

This hasn’t changed much from my days of using Visual Studio 2005/2008, or has it? I’m about to find out.

I click on New Project. On the left menu, I choose Templates > Visual Basic > Web. In the middle I choose ASP.NET Web Application. At the bottom I name it and click OK. The Template window should open. Choose Web Forms. If you don’t want to use Azure to host the app, uncheck the “Host in the cloud box”. Click OK. (If you left “Host in the cloud” checked, you’ll be prompted to enter information about your Azure account.)

When I’m using Visual Studio for app development, I keep the Toolbox pinned on the left side and the Solution Explorer pinned on the left.

# Step 3: Making a Web App

What I want to do is create a new “web page” where a user can enter some information and results will be returned. At the top, I select Project > Add New Item. On the left menu, I choose Visual Basic > Web > Web Form. At the bottom, I enter a name and click Add. (It should open in the middle pane, but if not, find it in the Solution Explorer, right-click, and select Open.)

I see HTML in the middle pane. I want to view the designer, so at the bottom of the window I choose “Split”. My VS now looks like this.

[<img class="aligncenter size-medium wp-image-4567" src="/wp-content/uploads/2016/06/VS-view-300x188.png" alt="VS view" width="300" height="188" srcset="/wp-content/uploads/2016/06/VS-view-300x188.png 300w, /wp-content/uploads/2016/06/VS-view-1024x644.png 1024w, /wp-content/uploads/2016/06/VS-view.png 1493w" sizes="(max-width: 300px) 100vw, 300px" />][2]

I want to use a stored procedure to show Purchase Order information. I’m going to add a GridView to the page, and connect it to a data source. In the Toolbox, I expand Data and find GridView. I click and drag it on to the bottom half, the design view.

<div id='gallery-3' class='gallery galleryid-4555 gallery-columns-2 gallery-size-thumbnail'>
  <dl class='gallery-item'>
    <dt class='gallery-icon portrait'>
      <a href='/wp-content/uploads/2016/06/VS-toolbox-gridview.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/VS-toolbox-gridview-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Choose GridView from the Toolbox" aria-describedby="gallery-3-4572" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-3-4572'>
      Choose GridView from the Toolbox
    </dd>
  </dl>
  
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/VS-designer-gridview.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/VS-designer-gridview-200x200.png" class="attachment-thumbnail size-thumbnail" alt="How the GridView will appear on the Designer" aria-describedby="gallery-3-4571" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-3-4571'>
      How the GridView will appear on the Designer
    </dd>
  </dl>
  
  <br style="clear: both" />
</div>

I need to connect it to my data source. Next to Choose Data Source, I click the drop-down arrow and the Data Source Configuration window appears. I choose “Database” and name the data source, then click OK.

The next step is creating the connection. I click “New Connection” and fill in the server information, click OK, then Next. I give my connection string a name and click Next. Now I have to choose what data I’ll show. I choose the option to “Specify a custom SQL statement or stored procedure” and click Next. I already created a stored procedure, GetPurchaseOrder, which I select. My stored proc has a parameter, StockItemID. I’m going to pass a value in from a text box. However, I haven’t added that to the form yet, so I can’t choose it. I leave Parameter source as None and click Next. I click the Test Query button and enter a parameter value. I get the results I expect. I click Finish.

<div id='gallery-4' class='gallery galleryid-4555 gallery-columns-2 gallery-size-thumbnail'>
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/DS-wizard-type.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/DS-wizard-type-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Choose &quot;Database&quot;" aria-describedby="gallery-4-4580" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-4-4580'>
      Choose &#8220;Database&#8221;
    </dd>
  </dl>
  
  <dl class='gallery-item'>
    <dt class='gallery-icon portrait'>
      <a href='/wp-content/uploads/2016/06/DS-connection.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/DS-connection-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Enter Server name, Authentication, and database name; Test Connection" aria-describedby="gallery-4-4579" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-4-4579'>
      Enter Server name, Authentication, and database name; Test Connection
    </dd>
  </dl>
  
  <br style="clear: both" />
  
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/ds-conn-string.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/ds-conn-string-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Name and save the connection string" aria-describedby="gallery-4-4578" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-4-4578'>
      Name and save the connection string
    </dd>
  </dl>
  
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/ds-config-select.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/ds-config-select-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Choose a custom statement" aria-describedby="gallery-4-4577" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-4-4577'>
      Choose a custom statement
    </dd>
  </dl>
  
  <br style="clear: both" />
  
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/ds-custom-statement.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/ds-custom-statement-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Choose stored procedure and select one from the list" aria-describedby="gallery-4-4576" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-4-4576'>
      Choose stored procedure and select one from the list
    </dd>
  </dl>
  
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/ds-parameters.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/ds-parameters-200x200.png" class="attachment-thumbnail size-thumbnail" alt="ds parameters" /></a>
    </dt>
  </dl>
  
  <br style="clear: both" />
  
  <dl class='gallery-item'>
    <dt class='gallery-icon landscape'>
      <a href='/wp-content/uploads/2016/06/ds-test-query.png'><img width="200" height="200" src="/wp-content/uploads/2016/06/ds-test-query-200x200.png" class="attachment-thumbnail size-thumbnail" alt="Click Test Query, enter a parameter value, and make sure you get the results you expect" aria-describedby="gallery-4-4574" /></a>
    </dt>
    
    <dd class='wp-caption-text gallery-caption' id='gallery-4-4574'>
      Click Test Query, enter a parameter value, and make sure you get the results you expect
    </dd>
  </dl>
  
  <br style='clear: both' />
</div>

I need to add that text box. First I drag a label from the Toolbox to the Designer. I change the (ID) and Text properties. Then I drag a TextBox control to the Designer, and again, change the (ID). Then, I add a Button to perform the search. I drag the Button over and change its (ID) and Text. Now, to tie the text box to the parameter, I click on my GridView so the tasks pop-out appears on the right, then click Configure Data Source. I click Next until I’m on the Define Parameters screen. For my Parameter source I choose Control. For ControlID, I choose txtItemId. I leave DefaultValue blank. I click Next and Finish.

<div id="attachment_4585" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/06/design-gridview-task.png"><img class="size-medium wp-image-4585" src="/wp-content/uploads/2016/06/design-gridview-task-300x90.png" alt="You can see the label and button in the top left. The GridView Tasks pop-out is to the right." width="300" height="90" srcset="/wp-content/uploads/2016/06/design-gridview-task-300x90.png 300w, /wp-content/uploads/2016/06/design-gridview-task-1024x310.png 1024w, /wp-content/uploads/2016/06/design-gridview-task.png 1238w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    You can see the label and button in the top left. The GridView Tasks pop-out is to the right.
  </p>
</div>

Now, I want to make sure this works. To get the web page to run, I click on Debug > Start Debugging (or F5). If all has gone according to plan, I’ll see my web page.

<div id="attachment_4584" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/06/web-page.png"><img class="size-medium wp-image-4584" src="/wp-content/uploads/2016/06/web-page-300x64.png" alt="Opened in Edge " width="300" height="64" srcset="/wp-content/uploads/2016/06/web-page-300x64.png 300w, /wp-content/uploads/2016/06/web-page.png 882w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Opened in Edge
  </p>
</div>

I enter a value and click Find. My results appear.

<div id="attachment_4586" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/06/web-page-results.png"><img class="size-medium wp-image-4586" src="/wp-content/uploads/2016/06/web-page-results-300x48.png" alt="The expected results appear " width="300" height="48" srcset="/wp-content/uploads/2016/06/web-page-results-300x48.png 300w, /wp-content/uploads/2016/06/web-page-results-1024x164.png 1024w, /wp-content/uploads/2016/06/web-page-results.png 1199w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    The expected results appear
  </p>
</div>

To end, I go back to VS and select Debug > Stop Debugging.

# A Basic Application

This is how I am going to test various database features! This sample is not:

  * Written using best practices
  * Going to perform well
  * Secure
  * Production-ready

It is, however, going to show me how things appear, and need to be configured, outside of SSMS. Exciting!

&nbsp;

 [1]: /wp-content/uploads/2016/06/Visual-Studio-Start-Page.png
 [2]: /wp-content/uploads/2016/06/VS-view.png