---
title: First look at SQL Server Management Studio Denali
author: SQLDenis
type: post
date: 2010-11-09T16:15:54+00:00
excerpt: 'The SQL Server Management Studio that ships with SQL Server Denali CTP1 is based on the Visual Studio 2010 shell. One of the advantages you will see right away is that if you have multiple monitors you can drag a query window to another monitor. This is&hellip;'
url: /index.php/datamgmt/datadesign/first-look-at-sql-server-management-stud/
views:
  - 23573
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - ctp
  - database
  - sql server denali

---
[<img src="http://farm5.static.flickr.com/4043/5152114334_94ec2fec08.jpg" width="470" height="306" alt="SSMS SplashScreen" />][1]

The SQL Server Management Studio that ships with SQL Server Denali CTP1 is based on the Visual Studio 2010 shell. One of the advantages you will see right away is that if you have multiple monitors you can drag a query window to another monitor. This is some great stuff because currently I have two and sometimes 3 instance of SSMS running at all times.

Here is an example, of three query windows which are floating, you can easily drag any of these to another monitor

[<img src="http://farm2.static.flickr.com/1426/5152114670_ee88704e09.jpg" width="461" height="500" alt="SSMS Docked Query Windows" />][2]

One thing that currently doesn&#8217;t work is that if you have a syntax error, you can&#8217;t click on the error in the messages tab anymore to jump to the line that caused the error. The closest thing you now have is the Error List, press on CTRL + , E to see it

[<img src="http://farm2.static.flickr.com/1340/5152114370_fd77c1caf7.jpg" width="500" height="237" alt="SSMS ErrorList" />][3]

The problem with the Error List is that it works on design/parse time, if you happen to get the same error as at execution time then you can click on the Error List item and it will bring you to the line with the error, otherwise you are out of luck.

## Object Explorer

Here is what Object Explorer looks like

[<img src="http://farm5.static.flickr.com/4059/5152114400_9fab5e36f0.jpg" width="290" height="500" alt="SSMS ObjectExplorer" />][4]

As you can see it is pretty similar to what was there before, there is a new folder called Sequences, you can read about sequences in this post here: [A first look at sequences in SQL Server Denali][5]

## Snippets

One cool thing is that you can now surround code with snippets. Here is what the process looks like. Highlight the code you want to surround, right click and select Surround With
  
[<img src="http://farm5.static.flickr.com/4029/5154291180_6c2aaf1dea.jpg" width="490" height="177" alt="Surround With" />][6]

Next pick if you want Begin, If or While
  
[<img src="http://farm2.static.flickr.com/1244/5154291192_0607ff75a2.jpg" width="429" height="94" alt="Surround With Begin" />][7]

Here is the final result
  
[<img src="http://farm2.static.flickr.com/1155/5154291210_ef77d5596d.jpg" width="293" height="111" alt="Snippet around code" />][8]

## SSISDB

In SQL Server Code Denali, you can deploy your Integration Services projects to the Integration Services server. The Integration Services server enables you to manage packages, run packages, and configure runtime values for packages by using
  
environments. Here is just a quick screenshot tour.
  
First thing you need to do is creating a catalog, right click on the Integration Services folder and select Create Catalog
  
[<img src="http://farm2.static.flickr.com/1406/5151504337_e483aa10a1.jpg" width="311" height="206" alt="SSMS Create Catalog Step1" />][9]

Add a password on the next dialog
  
[<img src="http://farm2.static.flickr.com/1150/5151504367_df05500747.jpg" width="500" height="339" alt="SSMS Create Catalog Step2" />][10]

Now your catalog is created, you will also see a new database in your Object Explorer in the Databases folder.
  
[<img src="http://farm2.static.flickr.com/1405/5152114502_a0e88c3cab.jpg" width="265" height="369" alt="SSMS Create Catalog Step3" />][11]
  
Do not delete this database, it will also delete the catalog

Create a new folder in the SSISDB catalog, right click on that folder and select Import SSIS Packages
  
[<img src="http://farm2.static.flickr.com/1118/5153893513_581462b3e5.jpg" width="342" height="145" alt="Import SSIS Packages" />][12]

This will bring up the following wizard
  
[<img src="http://farm2.static.flickr.com/1164/5154502760_ebd62c6541.jpg" width="500" height="454" alt="Integration Services Migration Wizard" />][13]
  
Since this is just a high level post, I am not going in detail about the Integration Services Migration Wizard, that is for a stand alone post.

I will leave you with a 3 minute video I took of me clicking around in the new SSMS. It has a HD version so make sure you click on that to get a better resolution

## Video [video:youtube:hWGG0s_qVX0] 

Click on the [SQL Server Denali][14] tag to see all our SQL Server Denali related posts

 [1]: http://www.flickr.com/photos/denisgobo/5152114334/ "SSMS SplashScreen by Denis Gobo, on Flickr"
 [2]: http://www.flickr.com/photos/denisgobo/5152114670/ "SSMS Docked Query Windows by Denis Gobo, on Flickr"
 [3]: http://www.flickr.com/photos/denisgobo/5152114370/ "SSMS ErrorList by Denis Gobo, on Flickr"
 [4]: http://www.flickr.com/photos/denisgobo/5152114400/ "SSMS ObjectExplorer by Denis Gobo, on Flickr"
 [5]: /index.php/DataMgmt/DataDesign/a-first-look-at-sequences-in-sql-server
 [6]: http://www.flickr.com/photos/denisgobo/5154291180/ "Surround With by Denis Gobo, on Flickr"
 [7]: http://www.flickr.com/photos/denisgobo/5154291192/ "Surround With Begin by Denis Gobo, on Flickr"
 [8]: http://www.flickr.com/photos/denisgobo/5154291210/ "Snippet around code by Denis Gobo, on Flickr"
 [9]: http://www.flickr.com/photos/denisgobo/5151504337/ "SSMS Create Catalog Step1 by Denis Gobo, on Flickr"
 [10]: http://www.flickr.com/photos/denisgobo/5151504367/ "SSMS Create Catalog Step2 by Denis Gobo, on Flickr"
 [11]: http://www.flickr.com/photos/denisgobo/5152114502/ "SSMS Create Catalog Step3 by Denis Gobo, on Flickr"
 [12]: http://www.flickr.com/photos/denisgobo/5153893513/ "Import SSIS Packages by Denis Gobo, on Flickr"
 [13]: http://www.flickr.com/photos/denisgobo/5154502760/ "Integration Services Migration Wizard by Denis Gobo, on Flickr"
 [14]: /index.php/All/sql+server+denali: