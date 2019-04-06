---
title: 'T-SQL Tuesday #50 – Automating Your Installs'
author: Brian Davis
type: post
date: 2014-01-14T20:54:32+00:00
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/t-sql-tuesday-50-automating-your-installs/
views:
  - 10514
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - automation
  - tsql2sday

---
<a href="http://sqlchow.wordpress.com/2014/01/07/t-sql-tuesday-050-automation-how-much-of-it-is-the-same/" target="_blank"><img class="alignnone size-full wp-image-2241" alt="TSQL2sday" src="/wp-content/uploads/2014/01/TSQL2sday.png" width="133" height="134" /></a>

This month’s T-SQL Tuesday is being hosted by SQLChow (<a href="http://sqlchow.wordpress.com/" target="_blank">b</a> | <a href="https://twitter.com/sqlchow" target="_blank">t</a>) and the topic is “Automation, how much of it is the same?”.  As SQLChow noted, automation was a topic a few years ago and now it is being revisited to see if and how it has changed over the years.

I know for me, over the last few years, automation has taken a larger role in many of the things I do.  I’ve automated many processes over the years using various tools such as SSIS and PowerShell and doing so has not only saved me time but my sanity as well  One of the biggest areas of automation for me over the past couple of years has been the automation my installs of SQL Server.  Everything from test and production server installs to automating the installation of the SQL Server Client Tools on developers machines and even cluster installs.

I know there are a couple tools out there to help with the automation of SQL Server installs, but my tool of choice has been <a title="SQL Server FineBuild" href="http://sqlserverfinebuild.codeplex.com" target="_blank">SQL Server FineBuild</a>.  SQL Server FineBuild is a CodePlex project that is freely available and provides a one-click install of SQL Server.  It supports all versions of SQL Server from 2005 through 2014 and is highly customizable.  Not only does it support the installation of SQL Server, it also supports service packs, cumulative updates and hotfixes as well as many 3rd party tools such as BIDS Helper.  SQL Server FinBuild gives you fine grain control over every aspect of your install from the database engine to reporting services, integration services, etc as well as configuration options such as ports, service accounts, database mail and even adding permissions for environment specific accounts or configuring the SQL Client Tools on a developers machine.  This is just a short list of what SQL Server FineBuild can automate.

With SQL Server FineBuild I’ve been able to automate my installs but the benefits haven’t ended there.  If I’ve needed another server setup identical to a current server for testing, it’s very simple to take the configuration file from the original server and use it to build the test server and know that the install will be identical to the original install.  I’ve also used SQL Server FineBuild to update current installs at different patch levels to a consistent patch level across the board.

Automating your installs, as with automating anything, takes some initial setup and testing time, but once complete the benefits out weigh the costs especially if you are doing a lot of installs.  Just automating the SQL Server Client Tools installation can be a large time saver as the install can then be run by almost anyone in support.  Not only that but every developer will be setup the same way and patched to the current level of your environment.

If there is a repeatable process that I have to do over and over, I try my best to automate it and with this tool I’ve been able to not only automate my installs but save time as well.  If you do a lot of installs or simply want a way to quickly reinstall a server in the case of a disaster, I encourage you to look into automating your installs.