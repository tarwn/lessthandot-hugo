---
title: SSMS – Enabling Ctrl+R Shortcut to show or hide query results/messages pane
author: Kevin Conan
type: post
date: 2013-03-04T14:58:00+00:00
ID: 2022
excerpt: 'Do you use Ctrl+R to hide or display the query results/messages pane in SSMS?  Have you installed SSMS 2012 and find that it does not work?'
url: /index.php/datamgmt/dbprogramming/mssqlserver/ssms-enabling-ctrl-r-shortcut/
views:
  - 16397
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - ssms

---
Do you use Ctrl+R to hide or display the query results/messages pane in SSMS? Have you installed SSMS 2012 and find that it does not work? Does it drive you crazy because you think they pulled keyboard shortcut or because in some installs it works but not in others?

Fear not, you can add this keyboard short-cut!

  1. In SSMS open the "Tools" menu and choose "Options...".
  2. On the left, expand "Environment", then expand "Keyboard" and then click on "Keyboard".
  3. Choose "Window.ShowResultsPane" (you can type that into the "Show commands containing:" field to find it faster. Choose "SQL Query Editor" from the "Use new shortcut in:" drop down list.
  4. Click into the "Press shortcut keys:" field and press Ctrl and R at the same time (the field should update to show "Ctrl +R".
  5. Review the "Shortcut currently used by:" dropdown list to ensure there are no other short cuts using Ctrl +R for SQL Query Editor.
  6. Click the Assign button and then the OK button.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/SSMSCtrlRSettings.JPG?mtime=1362416270"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/SSMSCtrlRSettings.JPG?mtime=1362416270" width="644" height="376" /></a>
</div>

And presto, the Ctrl+R functionality to hide or display the query results/messages pane in SSMS!