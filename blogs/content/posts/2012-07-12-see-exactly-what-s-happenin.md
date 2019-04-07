---
title: See Exactly What’s Happenin’ with SSIS 2012 Data Taps
author: joshuafennessy
type: post
date: 2012-07-12T20:49:00+00:00
ID: 1672
excerpt: |
  -- The Story --
  Experienced SSIS developers will recognize the usefulness of the simple  data viewer object used during the development process to peek into the  data set and see what is going on at various stages of execution.
  One piece of functional&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/see-exactly-what-s-happenin/
views:
  - 10962
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
  - SSIS

---
**&#8212; The Story &#8212;**_<img width="315" style="float: right;" src="http://joshuafennessy.files.wordpress.com/2012/07/dataviewer.jpg" alt="Yay!  Data Viewer!" height="315" />_

Experienced SSIS developers will recognize the usefulness of the simple _data viewer_ object used during the development process to peek into the data set and see what is going on at various stages of execution.

One piece of functionality that has been missing since SQL 2005 is the ability to do this from a production SSIS server. Since the data viewer required the use of BIDS, and most organizations don’t allow development tools to be connected to production; troubleshooting data issues often involved lots of blind poking around and vague assumptions.

This problem is nullified in SQL 2012 with the introduction of the SSIS Data Tap. This is a **run-time** utility than can be used to peek into the data at various stages of execution – JUST LIKE THE DATA VIEWER!

* * *

**&#8212; Information Collection &#8212;  
** 

Data tap files are output to the SSIS root install directory, and must be configured through SSMS. First some information about the package must be gathered in SSDT, so fire up your development environment and follow along.

1. Collect the **PackagePath**. To find this, start in the control flow and click on the **Data Flow** object that you want to tap. Copy the value of _PackagePath_

_![PackagePath Property Value][1]  
_ 

2. Collect the **IdentificationString**. To find this, enter the **Data Flow** object and select the **connector** that exists between the two transformations that you wish to tap. Copy the value of _IdentificationString  
<img width="372" src="http://joshuafennessy.files.wordpress.com/2012/07/clip_image004.png" alt="IdentificationString property value" height="27" />  
_ 

With these two pieces of information, it’s off to SSMS for the rest of the procedure!

* * *

**&#8212; Execution &#8212;  
** 

Connect to your SQL Server 2012 Instance that hosts the SQL 2012 Integration Services Catalog. Mine is SSISDB on my default instance. Once there, execute the following stored procedures in order.

catalog.create_execution (<a href="http://msdn.microsoft.com/en-us/library/ff878034.aspx" target="_blank">MSDN</a>)  
catalog.add\_data\_tap (<a href="http://msdn.microsoft.com/en-us/library/hh230989" target="_blank">MSDN</a>)  
catalog.start_execution (<a href="http://msdn.microsoft.com/en-us/library/ff878160" target="_blank">MSDN</a>)

To make it REALLY easy, I’ve written a SQL Script using template parameters that YOU CAN HAVE! All you need to do is use <CTRL>+<SHIFT>+M to replace the defaults with the values that are required for your instance. Get a copy of the script [here][2]. If you have trouble with the download [email me][3] and I’ll be happy to send it to you.

* * *

 **&#8212; Data Review &#8212;  
** 

After the package is executed, check your SSIS Install directory – mine is   
<span style="font-size: xx-small;"><strong><em>C:Program FilesMicrosoft SQL Server110DTS</em></strong></span> – for a subfolder called **DataDumps.** You should see a file there with the name specified in the catalog.add\_data\_tap stored procedure.

There is your output! Now you can see EXACTLY what SSIS was working with at any point of the execution, EVEN IN PRODUCTION!

* * *

**&#8212; Some Important Notes &#8212;  
** 

  * Data taps are only valid for ONE execution, so unless you create a job step with all of this code, you’ll only get a data tap when you explicitly specify it.
  * Data taps create I/O (go figure). This means that performance WILL be affected when running a data tap. I wouldn’t recommend running them for every execution. Only use them when needed.
  * Data taps use the catalog schema, which means they require an SSIS 2012 Integration Services Catalog to be used. Tapped packages also need to be deployed to this catalog.

 [1]: http://joshuafennessy.files.wordpress.com/2012/07/clip_image003.png
 [2]: http://sdrv.ms/PNTRDo
 [3]: mailto:josh@joshuafennessy.com