---
title: Quick Data Model creation off SSAS Data Source in SSRS
author: Ted Krueger (onpnt)
type: post
date: 2009-04-27T10:51:28+00:00
ID: 399
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/quick-data-model-creation-off-ssas-data/
views:
  - 18317
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
I'm a fan of not allowing connectivity from Excel to my SSAS instances. I just don't see the active connection ever being a truely manageable and secure connection. It remains though that the users like the way they do things and will fight hand and foot against change. Although it will be a hard task for you to remove the Excel connectivity to SSAS, you can provide them with a viable replacement given report models in reporting services.

The first time I mentioned to this to a developer, they thought inititally the task would take an exteremely long time and amount of work to get a report model up for the users. The task really only takes about 5 minutes though and here is how you do it.

Open report manager. If you do not already have a folder set aside for SSAS data sources, created one now. Name it “SSAS Data Sources” with a description of, “All connectivity to SSAS instances”. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//model_1.gif" alt="" title="" width="436" height="287" />
</div>

Once the folder is created, go into it and click the “New Data Source” button in the menu strip. In the new data source screen enter a meaningful name for the data source and give it a description. For connection type click the drop down and select Microsoft SQL Server Analysis Services.

like so...

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//model_2.gif" alt="" title="" width="436" height="287" />
</div>

In your connection string enter the connection to the SSAS instance and the SSAS database you want to read from.
  
Example:
  
Data Source=servername;Initial Catalog=”Sales DB”

Select the security model you wish to use. I typically use windows integrated for most connectivity to the instances so I can manage security with groups. This makes my db users and logins along with roles assignment easier and cleaner.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//model_3.gif" alt="" title="" />
</div>

Click ok and you should have your new DS in the folder we created. Open the data source again and you will now see a “Generate Model” button at the bottom of the configuration screen. Click it..

In the “generate new model” screen enter a meaningful name and description and click ok. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//model_4.gif" alt="" title="" />
</div>

Go into the security and configure it to fit into your security model. Click OK to create your data model.

That's it. Now open up report builder and select your new model. In my case I selected the sales cube located in my SSAS DB I connected to. Once you get into report builder off this connection you will see you basically have the functionality you need. 

Once the user creates the tables, matrix or charting objects, they can then export to Excel for more manipulation. The important thing is this will be a disconnected session in Excel now 🙂