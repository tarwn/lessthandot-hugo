---
title: The DBA.. their role, expectations and future?
author: Ted Krueger (onpnt)
type: post
date: 2009-04-15T10:56:08+00:00
ID: 388
url: /index.php/datamgmt/datadesign/the-dba-their-role-expectations-and-futu/
views:
  - 8737
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Microsoft SQL Server Admin

---
As promised from topic http://forum.ltd.local/viewtopic.php?f=17&t=4735&start=0&st=0&sk=t&sd=a&hilit=DBA I&#8217;m making a blog entry on my expectations of a DBA.

My reply to the question

**DBA in my eyes and how I perform the job is&#8230;**

  * Mid to Sr level TSQL abilities in order to fulfill business requirements in data access not directly available to developers. Personally don&#8217;t think a DBA is much if they don&#8217;t have at least mid-career skills in TSQL. They should be the ones telling the developers what not to write and how to write it. It will hurt them if they can&#8217;t.
  * Proactive monitoring of all resources including any instances currently installed in the environment. This includes development and test. Be able to fix them!!!
  * Proactive monitoring of performance bottlenecks. Blocking, deadlocks, memory pressure, load balancing due to pressure, index, statistics, usage of these. Load balancing references things like pushing reporting off main databases to prevent issues etc..
  * Create a stable security landscape. Schemas, user roles etc&#8230; and maintain it! If someone knows sa then they failed.
  * Knowledge and ability to install and configure &#8220;PROPERLY&#8221; any SQL Server service. SSRS, SSIS, SSAS etc.. If you are titled SQL Server DBA then you better know it&#8217;s not just a DB Service and know how to maintain and configure them all.
  * Ability to communicate high level DB Server issues to peers in less technical manner. Can you explain a deadlock and actually have a manager understand it? Important! You won&#8217;t get hardware upgrades without it.
  * Document everything. Landscape, security, service configurations
  * Ability to setup and configure a stable and trustworthy disaster recovery model.
  * Achieve at least a 98% up time goal during open and critical business transactions.

Finally, I think any DBA should show what they do in no less than a monthly report to upper management. I say upper sense typically this is where they report to. Monthly I send the directors a comprehensive report of performance of the production system. This including known issues and tasks open and or closed. Also contains project lists. The reason for this is one to show the business your worth and two, in order to maintain your db server landscape and have the ability to request upgrades and or scaling out you need to show and sell it. These reports can be critical to that IMO. If they can&#8217;t provide you with the current state of the database servers then they don&#8217;t know it and there is a point of failure.
  
\*\\*\*end reply\*\**

I thought of rewriting my reply but to be honest it sounds good as is. Copy/Paste went to work! I would like to add that a DBA shouldn&#8217;t be the type of DBA that corporate America typically brands them as. That is, people typically see a DBA as the quiet guy that has little to no interaction with the business. We&#8217;re all here for a reason and that&#8217;s to help business grow. Be more proactive in participating in that business and groups within it. Aspire to be more than a name on a paycheck. Be an asset!