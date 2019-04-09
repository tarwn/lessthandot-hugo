---
title: Wisconsin SQL Server User Group
author: Ted Krueger (onpnt)
type: post
date: 2010-10-11T10:11:44+00:00
ID: 921
excerpt: 'Tomorrow night I will be talking about Execution Plans for the Wisconsin SQL Server User Group.  This is a new session for me and I think the group and I will have a great time discussing how important it is to dig deep into plans.  Execution plans are without a doubt a point in the tuning process in which we can either make a database server live or land face first on the floor.'
url: /index.php/datamgmt/datadesign/wissug-execution-plans/
views:
  - 4760
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - execution plans
  - pass
  - sql server 2008
  - sql server user group
  - wissug

---
Tomorrow night I will be talking about Execution Plans for the [Wisconsin SQL Server User Group][1]. This is a new session for me and I think the group and I will have a great time discussing how important it is to dig deep into plans. Execution plans are without a doubt a point in the tuning process in which we can either make a database server live or land face first on the floor. 

The meeting is held in Waukesha, WI. 

**Address**: N19 W24133 Riverwood Dr., Suite 150, Waukesha, WI 53188 

To all my Illinois and #ChiSQL friends, the drive is actually short and an easy one. Take I-94 straight up and just west of Milwaukee. Check the construction schedules and detours of course. Milwaukee area like many other areas is in an ever changing battle with those orange cones and roads closing.

I hope to see everyone there! Let’s make the turnout a good one this month.

**Session Description**


<span class="MT_smaller"> 

<p>
  Have you ever had a query take FOREVER to execute? I have and it isn’t a good feeling. Often those queries still end up on Production simply because, “they work”. Let’s go behind the scenes of each query and address one of the largest factors in predicting how queries will perform, and even potentially lower the resource cost on the database server itself: Execution Plans.
</p>

<p>
  Beginning with SQL Server 2005 we are provided with a wealth of information about Execution Plans that have been executed. With this information we can finally tune queries based upon the exact process SQL Server takes to execute the commands we requested.
</p>

<p>
  This session will go from T-SQL Statement to Execution Plan. We will uncover high cost operations that are common yet easy to fix along with discussing some DMV statements that assist us in tuning queries. Our goals we will achieve together
</p>

<p>
  <strong>Our goals we will achieve together</strong>
</p>

<ul>
  <li>
    Understand how SQL Server processes statements
  </li>
  <li>
    Identify high cost operations in Execution Plans
  </li>
  <li>
    Common performance problems caused by poor Execution Plans
  </li>
  <li>
    Tuning with Indexes &#8211; altering existing indexes and maintaining indexes
  </li>
  <li>
    Tune SQL Server itself (such as Parallelism)
  </li>
  <li>
    Identify poor plans in the Execution Plan Cache
  </li>
</ul>

<p>
  </span>
</p>

 [1]: http://wisconsin.sqlpass.org/