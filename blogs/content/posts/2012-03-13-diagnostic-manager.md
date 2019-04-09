---
title: Diagnostic Manager – Viewing History outside of 8AM to 5PM
author: Kevin Conan
type: post
date: 2012-03-13T12:37:00+00:00
ID: 1558
excerpt: 'Prior to being a full-fledged DBA, I was a SQL Developer for a Warehouse Management System called HighJump.  HighJump is a very fast paced system with system owners who partner very closely with IT.  This also means, that I was working closely with peop&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/diagnostic-manager/
views:
  - 6870
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - diagnostic manager
  - idera
  - sql server

---
Prior to being a full-fledged DBA, I was a SQL Developer for a Warehouse Management System called HighJump.  HighJump is a very fast paced system with system owners who partner very closely with IT.  This also means, that I was working closely with people who were very sensitive any system issues and wanted answers fast.

I was just making the move from SQL Developer to DBA when I first learned of Idera's Diagnostic Manager.  My first task to was to learn more about this product that the DBA who I was replacing convinced my new boss to buy 10 licenses of.

So when I learned that Diagnostic Manager would record the state of the SQL Instance every “x” minutes (user defined), I was very excited.  This meant that I could go back in time to see what was happening during the “the system was slow between 5:00 and 5:30 yesterday morning”.

The next time I saw an e-mail asking what happened that morning around 4:00AM, I jumped into action.  I opened Diagnostic Manager and clicked on the SQL Instance in question and hit the History Browser window:

[<img style="border-style: initial; border-color: initial;" src="/wp-content/uploads/users/kconan/DM - History Browser Button.jpg?mtime=1331600719" alt="" width="476" height="130" />][1]

<p style="text-align: left;">
  On the right side of the screen, we can now navigate the Calendar to quickly go back to a specific day.  Because the issue I was looking at was from that day, I figured I just needed to scroll down to 4:00 AM and start looking at the different metrics.  Well imagine my surprise when I couldn't find anything from before 8:00 AM!  I looked at previous days and they only had data from 8:00 AM to 5:00 PM…
</p>

<div class="image_block" style="text-align: left;">
  <p>
    <a href="/media/users/kconan/DM - History Browser.jpg?mtime=1331600733"><img src="/wp-content/uploads/users/kconan/DM - History Browser.jpg?mtime=1331600733" alt="" width="234" height="452" /></a>
  </p>
</div>

<p style="text-align: left;">
  I starting thinking that I missed a setting when I added this Instance.  Maybe Diagnostic Manager is only captures data between 8:00 AM and 5:00 PM?
</p>

After looking around for what felt like hours, I sent Idera's Support an e-mail.  Idera's Support is top notch.  I got a reply within an hour with the exact details that I needed.

If you notice, in the above screenshot there is a blue link right below the Calendar that says “Between 8:00 AM and 5:00 PM”.  If you click on that link you will get this screen:

[<img src="/wp-content/uploads/users/kconan/DM - History Browser Options.jpg?mtime=1331600726" alt="" width="308" height="218" />][2]

Like most people, we are a 24/7/365 shop and I need to be able to look at snapshots from any point of time.  So I chose the top option “Show all snapshots”.  Now I can see all of my snapshots!

I encourage you to learn more about Idera and to start a free trial of Diagnostic Manager.

[https://twitter.com/#!/Idera_Software][3]

<http://www.idera.com/Content/Home.aspx>

 [1]: /media/users/kconan/DM - History Browser Button.jpg?mtime=1331600719
 [2]: /media/users/kconan/DM - History Browser Options.jpg?mtime=1331600726
 [3]: http://www.idera.com/Content/Home.aspx