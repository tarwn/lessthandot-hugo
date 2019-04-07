---
title: 'The Lazy DBA Series: Wizards!'
author: Ted Krueger (onpnt)
type: post
date: 2010-08-25T13:30:19+00:00
ID: 885
excerpt: "Today on the lazy DBA we're going to talk about why Gandalf couldn’t open the door to the mines of Moria.  OK, we're not going to talk about Gandalf or even Trolls.  Although some of us might work with a few Trolls.  We are going to talk about Wizards without pointy hats."
url: /index.php/datamgmt/datadesign/lazy-dba-sql-server-wizards/
views:
  - 8774
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database administration
  - import wizard
  - sql server
  - sqlpass

---
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazydba2.gif" alt="" title="" width="209" height="209" />
</div>

Today on the lazy DBA we&#8217;re going to talk about why Gandalf couldn’t open the door to the mines of Moria. OK, we&#8217;re not going to talk about Gandalf or even Trolls. Although some of us might work with a few Trolls. We are going to talk about Wizards without pointy hats. 

**Scenario**

A great way to discuss any topic is to put it directly in front of us with a real life situation. Take the following example…

_A DBA (Bilbo) has been working on a major upgrade to SQL Server 2008 for months. It has taken just about every minute of their time, given the number of database servers, the initiative to consolidate and being the only DBA employed. One morning the Director of Marketing (Arwen) comes to the DBA in a panic. See, Arwen forgot that in order to update every item for the company with the new marketing descriptions, she needed to give an Excel spreadsheet with them to Bilbo so he would import them._

What does Bilbo do? The company stands to lose hundreds of thousands of dollars when the competitor’s item descriptions catch the eye of potential buyers and business is lost.

  * Option 1
  
    Bilbo tells Arwen that he needs eight hours to develop an SSIS package in order to grab the Excel file, SELECT * FROM Sheet1$ into the buffer, merge that to another result set of the existing descriptions and item ID’s in order to successfully update each one row-by-row. Bilbo is an SSIS god too so the eight hours isn’t questioned.
  * Option 2
  
    Bilbo becomes an efficient, lean and mean DBA and pulls up the Import/Export wizard. Imports Sheet1$, writes a SELECT to JOIN the item master on the Sheet1$ by item ID, validates the match and changes SELECT to UPDATE. Oh yeah, this option takes 10 minutes.

**Picking the option**

I don’t think we need to discuss the efficient option here. Option 1 has all of the stable and set process steps that any import process should have. We bring data in from two sources, we match data and then we update based on the matches. This is an efficient method for a task that would be done daily, weekly and even monthly. Why isn’t option 1 all that great for the task at hand?

BIDS is an Integrated Development Environment and SQL Server Integrated Services is a platform that you should always use to manage imports and other tasks. The power is endless. However, the power can be misused as a bloated form of SSMS. One rule that I try to set when creating SSIS package for any task is, “Will I need to save this?” If I have no intentions of saving the finished product, then typically that package and SSIS was not required and I could have fully accomplished the task with other tools at my disposal. At the same time, I could accomplish the task quicker and more efficient while not debating on how to use SSIS for it. For clarification, the nice part of some of these wizards is, they use SSIS in the background to build packages as you work through them. This allowing you to save the wizards configurations as a package at the end.

**Wizards in SQL Server**

For a number of years, Wizards have invaded the functionality of all of the tools we have for SQL Server. Not only have they invaded, they have done so in large numbers. We have…

  1. Database Mirroring Configuration/Setup
  2. Replication Setup
  3. Import/Export
  4. Agent New Job 
  5. List goes on…

Replication is a big one on this list. I can’t remember the last time I set replication up without the wizard. Yes, I can do it in T-SQL with the supporting procedures (that get called behind the wizard), but why would I? Efficiency is a critical part in getting these tasks done.

**Option 2 played out**

I’m sure some SSIS masters are still saying that the package would take 10 minutes and that is the best route. Wizards are the devil and I only write T-SQL because I’m cool. OK, let’s open our minds just a little.

Option 2 played out…

Excel file comes in from Arwen

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazxywizard_1.gif" alt="" title="" width="362" height="311" />
</div>

Open SSMS and find your DBA database (that you should have in Simple recovery and is a place where you should do all of your godly DBA, Developer work)

Right click the DB&#8211;>Tasks&#8211;>Import Data…

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazxywizard_2.gif" alt="" title="" width="357" height="330" />
</div>

Select Excel as the source and find your XLS file

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazxywizard_3.gif" alt="" title="" width="467" height="116" />
</div>

Hit next and leave the destination as your DBA work area. Hit next again to go to the mappings definition. Check the first tab (or whichever the right sheet is)

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazxywizard_4.gif" alt="" title="" width="636" height="244" />
</div>

Name the table that you want to create and hit Next and then Finish

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazxywizard_5.gif" alt="" title="" width="547" height="450" />
</div>

Write up a quick SELECT with whatever filters are required and join it to the destination of the ITEMMSTR

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazxywizard_6.gif" alt="" title="" width="628" height="477" />
</div>

Then Alter the statement to make it an UPDATE

**Done!**

**Lazy is good but…**

Wizards can and will make you more efficient at your job. Of course wizards can only do so far and you will find them restrictive when you have much more logic and conditions that need to be applied to the situation and task at hand. That is the point though. The combination of using wizards to get certain tasks done faster with the same stability will allow for the time needed on the bigger tasks that require a full assault approach.