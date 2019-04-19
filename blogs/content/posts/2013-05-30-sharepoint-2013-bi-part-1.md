---
title: Building a SharePoint 2013 BI Demo Environment Part 1 – Introduction
author: Koen Verbeeck
type: post
date: 2013-05-30T06:45:00+00:00
ID: 2088
excerpt: "Recently I had to give a Power View demo for a client. Giving a demonstration means of course you have a demo environment available. I could've simply given a demo on Power View using Office 2013 and call it a day, but I really wanted to show the “conve&hellip;"
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-1/
views:
  - 59887
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Excel Reporting
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSAS
  - SSRS
tags:
  - business intelligence
  - demo environment
  - sharepoint 2013
  - sql server
  - syndicated

---
<p class="MsoNormal" style="text-align: justify;">
  Recently I had to give a Power View demo for a client. Giving a demonstration means of course you have a demo environment available. I could've simply given a demo on Power View using Office 2013 and call it a day, but I really wanted to show the <em>“convert Power View report to PowerPoint”</em> feature, which is only available in Power View for SharePoint. And also because I wanted to write this blog post series of course.
</p>

<p class="MsoNormal" style="text-align: justify;">
  Since I hadn't a demo environment with SQL Server 2012 and SharePoint 2013 available, I decided to build one from scratch and share all the steps I've taken in these blog posts. This series will be a lot like the one by <a href="http://www.bimonkey.com/2012/04/build-your-own-sql2012-demo-machine-part-1-preparation-summary/">BI Monkey</a> (<a href="http://www.bimonkey.com/">blog</a> | <a href="https://twitter.com/BI_Monkey">twitter</a>), except that I'll be using SharePoint 2013 and SQL Server 2012 SP1 features. Furthermore, I didn't set-up a domain controller because I didn't feel the need for it in a small demo environment. Also, I wanted to test out if it was possible to set-up such an environment missing a demo controller without SharePoint going into a tantrum. For demo environments used by more than 1 user or actual test/production environments, I really encourage you to configure a proper domain. For instruction on how to do this, read the blog post by BI Monkey: <a href="http://www.bimonkey.com/2012/04/build-your-own-sql-2012-demo-machine-part-3-installing-and-configuring-windows-server-2008r2/">Build your own SQL 2012 Demo Machine – Part 3 – Installing and Configuring Windows Server 2008R2</a>.
</p>

<p class="MsoNormal" style="text-align: justify;">
  The demo environment will be created on a single VM, so it is easy to bring on an external disk or easy to exchange between colleagues who will also be giving demos. I will use Hyper-V for the virtual environment, but most or even all functionality can also be done with Oracle VirtualBox.
</p>

<p class="MsoNormal" style="text-align: justify;">
  <strong>The Hardware</strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  If you want to run a single server environment like this one on your laptop, you better have the necessary hardware available. I have a 250GB SSD drive where the VM will be stored, 32GB of RAM and an Intel Core i7 processor with in total 8 cores. If you don't have a laptop like this available (especially the amount of RAM is important to run SharePoint smoothly), you could option to create your environment in the cloud. That way you always have your environment available to you, provided you have an Internet connection.
</p>

<p class="MsoNormal" style="text-align: justify;">
  <strong>The Software</strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  I'll be using SQL Server 2012 SP1 Developer Edition, SharePoint Server 2013 Enterprise edition and Office 2013 Professional Plus.
</p>

<p class="MsoNormal" style="text-align: justify;">
  <strong>Summary</strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  The series consists of the following parts:
</p>

<ul style="margin-left:20pt;list-style-position:outside;">
  <li>
    <span style="text-indent: -18pt;" lang="EN-US">Part 1 – Overview (what you're looking at right now)</span>
  </li>
  <li>
    <span style="text-indent: -18pt;"></span><span style="text-indent: -18pt;">Part 2 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-2">Building the VM</a></span>
  </li>
  <li>
    <span style="text-indent: -18pt;">Part 3 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-3">Installing the OS</a></span>
  </li>
  <li>
    <span style="text-indent: -18pt;">Part 4 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-4">Installing SQL Server</a></span>
  </li>
  <li>
    <span style="text-indent: -18pt;">Part 5 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-5">Installing SharePoint</a></span>
  </li>
  <li>
    <span style="text-indent: -18pt;">Part 6 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-6">Installing PowerPivot for SharePoint</a></span>
  </li>
  <li>
    <span style="text-indent: -18pt;">Part 7 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-7">Configuring SharePoint</a></span>
  </li>
  <li>
    <span style="text-indent: -18pt;">Part 8 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-8">Verifying PowerPivot Integration</a></span>
  </li>
  <li>
    <span style="text-indent: -18pt;">Part 9 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-9">Installing Reporting Services</a></span>
  </li>
  <li>
    <span style="text-indent: -18pt;">Part 10 – <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-10">Creating a BI site</a></span>
  </li>
</ul>

<p class="MsoNormal" style="text-align: justify;">
  The links to these parts will be updated when the series progresses.
</p>

<p class="MsoNormal" style="text-align: justify;">
  Next part: <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-2">Building the VM</a>.
</p>