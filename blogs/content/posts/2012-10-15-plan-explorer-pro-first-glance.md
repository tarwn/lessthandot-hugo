---
title: Plan Explorer Pro – First Glance
author: Ted Krueger (onpnt)
type: post
date: 2012-10-15T10:59:00+00:00
excerpt: 'Over these past few days, I’ve had a chance to check out the new release of Plan Explorer Pro from SQL Sentry.  I’d like to thank them again, for giving me the chance to dig into this new edition and make full use of the additional features it has to of&hellip;'
url: /index.php/datamgmt/datadesign/plan-explorer-pro-first-glance/
views:
  - 7492
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Over these past few days, I’ve had a chance to check out the new release of [Plan Explorer Pro][1] from [SQL Sentry][2].  I’d like to thank them again, for giving me the chance to dig into this new edition and make full use of the additional features it has to offer.  As with any software, there are pros and cons to what you can do with it.  Let’s face it, not everyone can be fully satisfied 100% of the time.  That is, however, the cool part of the evolution of software tools; evolving to meet that 100% goal of full satisfaction across the board.  With Plan Explorer Pro, I honestly had a hard time finding anything to really throw on the con list.  That is probably due in part to the free version of Plan Explorer already being one of my valued and most effective tools I have in my bag of tools to assist in how I perform daily tasks as well as enhance demonstrations and training sessions.  For today, let’s take a quick look at a few of the features the Pro edition has to offer and why I think it&#8217;s well worth, if not worth more, the cost it takes to land it installed on your machine(s).

We’ll focus on the features I have gotten into so far as shown below 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/plan_exp_1.gif?mtime=1350304970"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/plan_exp_1.gif?mtime=1350304970" width="624" height="105" /></a>
</div>

</a>

**Multi-Tab Interface**

This may be small to most but, efficiency is absolutely critical to my work.  I’ve talked about his for years in presenting and mentoring; lazy efficiency.  Lazy efficiency requires the right tools at your mouse pointer.  Such things as wizards that function, multiple monitors or even a mouse that functions the way you need it.  Remember the case study done way back referred to as, “Death by the 10 second waits”?  That study, if I recall accurately, was done based on multiple monitor utilization.  In all, the time saved by ALT+TAB or however switching between screens is performed, is increased dramatically by the addition of multiple monitors.  Window tabs fall into this area.  Plan Explorer Pro now has a tabbed format for windows.  I will admit, having a dozen unique Plan Explorer instances running while working was annoying.  Now, with Pro, we tab it!

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-164.png?mtime=1350304971"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-164.png?mtime=1350304971" width="624" height="104" /></a>
</div>

As shown above, I have three distinct plans open and I&#8217;m working on them at the same time.  This is absolutely excellent in the terms of efficiency.  Given the normal task in which several plans could all relate but in the scheme of things, they are unique and thus require tuning in their own window, this is a big time saver.  There is one con to this that I found but will save that for the con area even more so since it’s the only con I found.  (One con alone says something about this great tool.)

**Sessions, History, and Comments**

The next thing I found really valued in the Pro Edition, the history and comments area.  At first, I didn’t care all that much about this feature.  However, I saw the benefit simply from utilizing the feature.   Essentially, while tuning and changing things during that process such as indexing, T-SQL alterations or just commenting on what needs to happen, you can track that evolution in the history and comments area.  What is even better is, it will add to the history area upon each time you alter the statements you are working on.  For example, I write a statement against AdventureWorks2012, to work with the new features that Pro Edition has.  During the process of tuning the statement, I managed to retain a historic view and notes of why I did what I did, as well as what was changed.  Personally, this is critical to tuning; keeping track of what you changed.  As you inadvertently will at some point, change something and not remember why.  When that change affects other queries or batches, you’ll wish you knew when and where it was made.  As shown below, the history and comments area is simply that, historic changes notes and an area to comment for each.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-165.png?mtime=1350304971"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-165.png?mtime=1350304971" width="316" height="299" /></a>
</div>

**Full Query Call Stack** 

The next feature, Full Query Call Stack, is something I’ve been hoping for in Plan Explorer for some time.  Before, tuning a batch was a bit difficult as it pertains to cost and specific issues that go along with aspects to the entire transaction.  For example, a loop is something we can now look closely at.  Take the example query I wrote to test out Pro edition.

<pre>DECLARE @LOOP INT = 1
DECLARE @DueDate DATETIME
SET @DueDate = (SELECT MAX(DueDate) FROM Sales.SalesOrderHeader)

WHILE @LOOP < 100
 BEGIN
	SELECT DueDate, ShipDate FROM Sales.SalesOrderHeader WHERE DueDate = @DueDate 
  SET @LOOP += 1
 END</pre>

With Pro Edition, we can now follow through the loop and each iteration as it pertains to the cost of that iteration.  In the example query, this equates to 101 generations of cost in terms of a plan to review; the first assignment of the variable @DueDate and then the 100 iterations of the loop.  In the real world, this could provide quick examination of problems that are dug deep in a long process or transaction, such as a procedure call hundreds of lines down in a conditional statement.  Now, that line can be pointed out quickly based on review of runtime based on the cost and Plan Explorer’s way of highlighting the high cost operations.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-166.png?mtime=1350304971"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-166.png?mtime=1350304971" width="624" height="64" /></a>
</div>

In the above image, the highlighted row could be a point in which the full query stack would be extremely useful.  This line is pointing out that the step-through to this point hits a statement that throws a cost of 76 reads on an Index Scan and high duration and CPU as it pertains to the total time of execution.  Clicking the line populates the Plan Tree tab and shows more depth to the tuning area identified.

In this case, I might want to add an index and then comment on the history area that I am doing so to tune this portion of the script.

<pre>CCREATE INDEX IDX_DueDate_ASC ON Sales.SalesOrderHeader (DueDate)
GO</pre>

Clicking the actual plan again in Plan Explorer Pro, I drop my reads to 2 and remove the high cost on the other areas.  Again, I would want to add to the comments that the area was successfully tuned base on the index addition.

Stepping through the stack, another statement is seen as high cost to the overall runtime.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-167.png?mtime=1350304972"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-167.png?mtime=1350304972" width="624" height="57" /></a>
</div>

Again, we’d want to add to the comments, another index change is needed.

<pre>CREATE INDEX IDX_DueDate_ASC ON Sales.SalesOrderHeader (DueDate) INCLUDE (ShipDate)
WITH (DROP_EXISTING=ON)
GO</pre>

After this change, life is good in query land with another lowered overall reads to 2 and the key lookup removed.  Notice the key lookup shown in the last image is removed after you change the index.  I always found this really cool in Plan Explorer; you only see what you need to in order to tune the statements.  Essentially, the bad is provided so we can focus on the areas and get moving along.  That makes a good tool and puts value in it.  Sifting through blank columns goes back to voiding the lazy efficiency concept.

**Wait Statistics Tab**

The last feature I’ll go over today is the Pro Edition feature, waits statistics tab.  This, being a DBA type in the SQL Server world, is the “shiny”.  This will show the accumulated wait stats for the given execution of the statement.  Imagine knowing as you go into a deployment, the exact cost in the form of waits that you will be introducing without having to capture periodic result from sys.dm\_os\_wait_stats.  I shouldn’t have to say much more on the value in this to providing cost of a query to a system, scalability of a statement on the system and even, showing of statistics for required upgrade or hardware needs.

For example, changing the statement that has been used to perform and table wide update should prove to show high shared page IO latch wait times.

<pre>DECLARE @LOOP INT = 1
DECLARE @DueDate DATETIME
SET @DueDate = (SELECT MAX(DueDate) FROM Sales.SalesOrderHeader)

WHILE @LOOP < 100
 BEGIN
	UPDATE Sales.SalesOrderHeader 
	SET DueDate = @DueDate
	WHERE DueDate = @DueDate
  SET @LOOP += 1
 END</pre>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-168.png?mtime=1350304972"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-168.png?mtime=1350304972" width="299" height="116" /></a>
</div>

As expected, the PAGEIOLATCH_SH wait type is shown in the new tab that appears, Wait Stats.  I cannot stress enough the usefulness in this alone.  This really was the wow to me in the Pro Edition.

**Cons**

<span class="MT_red"><strong>Update</strong>: As with all great companies, SQL Sentry went beyond my expectations and took the below remarks on the add-in and updated the functionality to work with the new multi-tab features. Please read, “<a href="/index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/ssms-add-in-for-plan">SSMS Add-in for Plan Explorer Update</a>” on the update and really, ignore the &#8220;con&#8221; listed below. I do want to leave it because it really shows how much SQL Sentry takes into account who their customers are and the feedback they get into making their products fit what the customers are looking for. </span>

<del>Not everything can be perfect and everything can be improved upon.  There is no way around that. If that wasn’t the fact, we’d only have one database engine, one programming language, one tool to rule them all (and in the assembly bind them), and one release with no revenue for anyone.  With that, the one con that I found and was a bummer, if you will, with the new tab feature; the SSMS add-in will not look for an active Plan Explorer open and add it as a tab.  I hope that may be something we seen soon in an update to Pro Edition.  I cannot speak for everyone but I rely on opening Plan Explorer from SSMS more than actually opening plans as a file or old plans I was working on.  I also tried dragging tabs to other windows but they are owned by the instance of Plan Explorer they were opened in so that does not work.</del>

**Summary**

Given the one Con vs. the many Pros I found in the short time I’ve had Plan Explorer Pro, the product is easily valued more than the cost.  Plan Explorer has become a tool I’d hate to be without while tuning execution plans and with the Pro Edition release, the additional features have proved to make the tool even more essential to how we work with execution plans, the effects they have as complete runtime batches and at an extremely low cost.

I highly recommend downloading the Pro Edition and trying it out.  The return of investment after reviewing the Pro Edition is fairly direct in the cost savings of time while you perform the tuning that goes into every installation of SQL Server.

 [1]: http://sqlsentry.net/plan-explorer/sql-server-query-view.asp
 [2]: http://www.sqlsentry.net/