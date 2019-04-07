---
title: Send Excel formatted query results on schedules
author: Ted Krueger (onpnt)
type: post
date: 2009-04-10T10:56:51+00:00
ID: 381
url: /index.php/datamgmt/datadesign/send-excel-formatted-query-results-on-sc/
views:
  - 15820
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Managers and users love Excel. We cannot get around it, no matter how horrid it is to load and work with Excel from a systems stand point. In the goals of automating our lives so we can be lazy DBA&#8217;s, you&#8217;re going to want to look into Database Mail to do this type of task. Why not? Database Mail is one of the best upgraded features of SQL Server 2005+. Remember what we went through to do something as simplistic as sending an email prior? Data gods help us if we had to attach a file. Oh man it was hell! Database Mail is awesome and having the @query_&#8230;. parameters is even more awesome, but it&#8217;s not the quickest and best way to do this task. I don&#8217;t find it to be that great unless my data is so dynamic I have little choice. Remember that reporting platform they were so kind to give us called Reporting Services? Yeah, if it&#8217;s not up and running somewhere then get it running. SSRS is more than just a point, click, run my report, make VP happy and out of my hair. You have email capabilities with SSRS by creating subscriptions. The best part is you have options to manipulate report results into a ton of formats. By default you should see csv, web, pdf, excel and a few others. You can even add more by configuring SSRS in depth. So why be the &#8220;Cool DBA or Developer&#8221; and script all that junk out? I like being lazy. I get more done 😉

First and if you haven&#8217;t done so already, install SSRS from the SQL Server installation. Make sure you reapply any service packs or cumulative updates. Then while configuring SSRS make sure you go into the &#8220;E-mail Settings&#8221; and configure it as your environment dictates. 

Create the output the users require as a SSRS report. Key is to format it as you need it in Excel. Don&#8217;t forget, what you make the report look like will be how it looks like in Excel. Charts and all. That&#8217;s the power of it!

Now that you have a report I prefer to configure subscriptions via the instance from SSMS over the report manager. Bad thing is in 2008 this changed drastically. It isn&#8217;t as easy as it once was. Not sure why they did this but they did and we have to adapt. 2008 utilizes the shared schedules over digging to the subscriptions options from the Home node. In 2008 you will find report manager much easier to deal with over SSMS in my opinion and mostly because you lost options from SSMS over manager. In 2005 I still like SSMS.

So for 2005 connect to the SSRS instance. Expand Home to the report and expand subscriptions. Right click and add new subscription. Fill in the options you need and then select Excel for the Render Format.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//subscription_options.gif" alt="" title="" width="704" height="628" />
</div>

You&#8217;re done with that massive amount of work you told your manager you&#8217;d have to do in order to email the daily KPI&#8217;s as Excel attachments. Now take a week to be lazy cause that&#8217;s how much you said you needed. 

I know I didn&#8217;t go into this much and how to do it. Skipped installing SSRS, creating a report, making it Excel friendly and a bunch of things you may have trouble with at first. Point is, when you get things going you can create a email process to attach data loaded to Excel in seconds over fighting with scripts and other email methods or even worse, create a executable or external process. Sometimes thinking of all the objects and systems you have at your disposal to simplify tasks can be hard to see when early in your career. You would be surprised at how many people I have seen ignore this method and others over huge scripting adventures.