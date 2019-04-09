---
title: Database Projects – Setting up Source Control
author: Axel Achten (axel8s)
type: post
date: 2012-12-11T12:38:00+00:00
ID: 1827
excerpt: |
  For some time I wanted to document the possibilities of the SQL Server Database Project and how developers and DBA's can benefit from it.
  Problem Statement
  Many companies I've worked for struggle with their database lifecycle management:
    
  Producti&hellip;
url: /index.php/datamgmt/dbadmin/database-projects-setting-up-source/
views:
  - 7515
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - ankhsvn
  - database lifycycle
  - database project
  - sql server
  - sql server data tools
  - ssdt
  - subversion

---
For some time I wanted to document the possibilities of the SQL Server Database Project and how developers and DBA's can benefit from it.
  
**Problem Statement**
  
Many companies I've worked for struggle with their database lifecycle management:

  * Production databases are copied to Test, Staging, UAT… environments by the DBA's with production (sensitive) data
  * DBA's spend too much time doing the copy operations
  * Backup chains are broken because of the lack of use of the WITH COPY_ONLY option
  * Changes are made by the DBA executing individual scripts, sometimes breaking because the development database and production database were out of sync.
  * Bugs are “Emergency” fixed in the production database so the schemas of the development and production database are out of sync
  * …

**Possible solution**
  
In general there is not much to learn from a developer (just kidding!) but normally they have some software lifecycle management and typically use a Source Control Provider to keep version information and the production version of their software. With SQL Server Data Tools and using a Database Project we should be able to setup a similar lifecycle management for our databases. In my next series of posts I will explore the possibilities of the Database Project and share them with you.
  
**Getting started &#8211; Choosing a Source Control Provider**
  
Again as a DBA I did not know what Source Control Provider to choose, I worked with Visual Source Safe a decade ago and had to do a setup of Team Foundation Server once and didn't want to go through this pain again. A quick poll on twitter on which Source Control Provider to use was depressing. Probably because our American friends were sleeping, the Belgian Developers were still hitting the snooze button and Christiaan was walking his dog. Lucky for me Dave Dustin ([twitter][1]) from the other side of the world gave me some possibilities. In the next example I will use AnkhSVN, Subversion with Support for Visual Studio. You can download the setup from [here][2].
  
**The setup**
  
Launch the installer, accept the license agreement and hit Install:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPSetup1.png?mtime=1355235876"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPSetup1.png?mtime=1355235876" width="250" height="194" /></a>
</div>

Off course you have to allow the program to install software:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPSetup2.png?mtime=1355235894"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPSetup2.png?mtime=1355235894" width="232" height="121" /></a>
</div>

Et voila, the setup is finished:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPSetup3.png?mtime=1355236334"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPSetup3.png?mtime=1355236334" width="250" height="194" /></a>
</div>

When you open SQL Server Data Tools and open the Options pane in the Tools menu you should see that AnkhSVN &#8211; Subversion Support for Visual Studio is selected as the Current Source Control plug in, if not you can select it from the list:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPSetup4.png?mtime=1355235963"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPSetup4.png?mtime=1355235963" width="373" height="215" /></a>
</div>

Now that we have a Source Control provider we can get started with our database project but this will be the subject of my next post.

 [1]: https://twitter.com/venzann
 [2]: http://visualstudiogallery.msdn.microsoft.com/E721D830-7664-4E02-8D03-933C3F1477F2