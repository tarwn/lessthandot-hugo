---
title: 'SQL Advent 2012 Day 22: Testing your backup and failover strategy'
author: SQLDenis
type: post
date: 2012-12-22T21:31:00+00:00
ID: 1879
excerpt: This is day twenty-two of the SQL Advent 2012 series of blog posts. Today we are going to look at how to test your backup and failover strategy
url: /index.php/webdev/business-intelligence/testing-your-backup-and-failover/
views:
  - 20061
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
tags:
  - backup
  - restore
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - testing

---
This is day twenty-two of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at how to test your backup and failover strategy

Let&#8217;s say your CEO comes to you and asks if your backups are good, you say yes, the CEO proceeds to tell you that the board will be arriving in 5 minutes and he will do a hard unplug of your main server. Have the backup restored within 1 hour. How comfortable are you now? Do you actually even test your backups, how do you know that they are not corrupt? What about failover to the other data center, has this been tested, do you know that it will work? 

With Hurricane Sandy causing havoc a couple of weeks ago where whole data centers ran out of fuel and generators didn&#8217;t start up, there are a whole bunch of companies rethinking their HA/DR strategy. Is it really wise having your data center not enough geographically dispersed. I mean if your main data center is in New York City and your backup data center is in Jersey City or Secaucus then you will be in trouble when a storm like Sandy comes along.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Storm.PNG?mtime=1356218425"><img alt="" title="Hi I am Sandy...I am here to destroy your backups" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Storm.PNG?mtime=1356218425" width="560" height="328" /></a>
</div>

One way I test backups is that I keep a hot backup server for some of the databases that are not changing as frequently as others. I basically have SQL jobs that backup the databases from one server straight to the other server, then a restore is done, permissions are fixed and checkdb is run. Another way I test the backups with some bigger databases is that I do restores to our staging server once a week or so, these restores take about 6 hours or so.

You need to test restoring full backups, you also need to test applying differential and transaction log backups. Ideally you want this automated.

Where do you store your backups? Next to the server in a bin are all the tapes? Bad idea. You need to store the backups offsite or be doing backups to the other datacenter. Your backups cannot be _only_ in the same location as the server that you are backing up from.

See also this post by Ted Krueger for some more backup information: [Backups are for sissies!!!][2]

We have a 200 page or so document that explains in detail what needs to happen if we switch data centers or if we have to rebuild a whole data center in the case of a catastrophe. How long would it take you to rebuild a server, install SQL server, installing all the apps and making sure that all the permissions are correct&#8230;&#8230;.oh what&#8230;oh you didn&#8217;t think you needed to backup master and tempdb. Hopefully you have all your jobs and SSIS packages scripted out or backed up. What about the permissions and accounts, do you know all the accounts that you need to create in case you don&#8217;t have a master backup?

When you boss asks next time what you do all day, make sure to tell him or her that you are making sure that in the case of a catastrophe the company is back in business in an instant, it is part of your job and your duty to yourself and the company

That is all for day twenty-two of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][3]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/backups-are-for-sissies
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap