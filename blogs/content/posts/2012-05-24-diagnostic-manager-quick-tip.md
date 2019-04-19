---
title: Diagnostic Manager Quick Tip – Job Messages
author: Kevin Conan
type: post
date: 2012-05-24T09:39:00+00:00
ID: 1615
excerpt: "A great use of Idera's Diagnostic Manager is to manage your SQL Job's and knowing which jobs have failed, which ones are running long and which ones have been running for the last 10 hours.  You even get to adjust the thresholds for each all of those me&hellip;"
url: /index.php/datamgmt/dbadmin/diagnostic-manager-quick-tip/
views:
  - 5351
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin
tags:
  - dba
  - diagnostic manager
  - idera
  - sql

---
A great use of Idera's Diagnostic Manager is to manage your SQL Job's and knowing which jobs have failed, which ones are running long and which ones have been running for the last 10 hours. You even get to adjust the thresholds for each all of those metrics.

If a job fails, you can don't have check the history of the job in SSMS to see why it failed. Instead you can do it right from Diagnostic Manager. From SQL Agent Jobs in the Services section, find a click on the job you want to know the history of in the top section.

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/DMJobMessage1.jpg?mtime=1337826030"><img alt="" src="/wp-content/uploads/users/kconan/DMJobMessage1.jpg?mtime=1337826030" width="1128" height="385" /></a>
</div>

The history will be displayed on the lower section and you can expand each run of the job. Find the failed execute of the job that you want more information about and expand it. If you hover over the message it will pop up displaying the whole message. The problem is that it disappears really fast and can be annoying or hard to read if it is long.

So here is today's tip! Right click on the message and choose “View Message”. Now the message opens in a pop up window that you can change the size of, highlight and copy and paste the details. When you are finished reading it, you can close it.

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/DMJobMessage2.jpg?mtime=1337826030"><img alt="" src="/wp-content/uploads/users/kconan/DMJobMessage2.jpg?mtime=1337826030" width="905" height="441" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/DMJobMessage3.jpg?mtime=1337826031"><img alt="" src="/wp-content/uploads/users/kconan/DMJobMessage3.jpg?mtime=1337826031" width="758" height="363" /></a>
</div>