---
title: Diagnostic Manager Quick Tip – New Server Tag
author: Kevin Conan
type: post
date: 2012-06-04T23:59:00+00:00
ID: 1645
excerpt: 'Where I work, we like to buy Diagnostic Manager Licenses in batches.  So every quarter or so I add in a bunch of new instances.  When you start getting over 25 instances being monitored, it gets tricky to remember which 5 instances you just added into t&hellip;'
url: /index.php/datamgmt/datadesign/diagnostic-manager-quick-tip-new/
views:
  - 5676
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - dba
  - diagnostic manager
  - idera
  - sql

---
Where I work, we like to buy Diagnostic Manager Licenses in batches. So every quarter or so I add in a bunch of new instances. When you start getting over 25 instances being monitored, it gets tricky to remember which 5 instances you just added into the mix.

One solution that I like to use, is to create a "New Server" tag and slap it on each server as it add it.

Let's just right in and create the "New Server" tag. From the File Menu choose Manage Tags.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/tag1.jpg?mtime=1338861169"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/tag1.jpg?mtime=1338861169" width="231" height="150" /></a>
</div>

Near the bottom of the Manage Tags screen, choose Add.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/tag2.jpg?mtime=1338861170"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/tag2.jpg?mtime=1338861170" width="578" height="544" /></a>
</div>

Enter "New Server" into the Name field and click on ok.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/tag3.JPG?mtime=1338861170"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/tag3.JPG?mtime=1338861170" width="359" height="121" /></a>
</div>

Now as you add servers, assign them to the "New Server" tag and then you can filter on them!