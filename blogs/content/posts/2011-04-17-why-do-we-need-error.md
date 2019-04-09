---
title: Why do we need error handling?
author: SQLology ~ Kim Tessereau
type: post
date: 2011-04-17T06:00:00+00:00
ID: 1118
excerpt: |
  Why do we need error handling?
   
  Error handling is something that is often forgot or misunderstood or put off until something goes awry and we try to back peddle and retro fit the error handling logic in the script, stored procedure or function. 
   &hellip;
url: /index.php/datamgmt/dbprogramming/why-do-we-need-error/
views:
  - 13938
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server

---
**Why do we need error handling?**

 

Error handling is something that is often forgot or misunderstood or put off until something goes awry and we try to back peddle and retro fit the error handling logic in the script, stored procedure or function. 

 

I think there are quite a few reasons we should engage in handling something other than the happy path of logic.  I’ve listed a few here and I’m sure you can think of some more.

 

**Inform calling process of situation: ** I’m going to pretend, just for a short moment, that I’m a developer.  Let’s say I’m tasked with the job of writing some new functionality in an existing system.  I want to be efficient and reuse some of the existing stored procedures already written in the system.  So I start looking at some of the logic with the procs and I realize that I can’t tell what the business rules are for a given process because there isn’t any controls, error handling, that helps me understand what is acceptable as far as data values are concerned.  So the calling process that I would be writing would only know about the happy path and not the so called “roads less traveled”.

 

**Assist in handling transactions:**  The first thing that comes to mind when I think about a transaction is an old example of the ATM machine transactions.  Let’s say that you are going to transfer $100 from your checking account to your saving account.  If you looked at the code to do this it would probably look something like this….

 

     UPDATE checking

            SET balance = balance &#8211; @transfer_amount

            WHERE account = @checking_account

 

     UPDATE savings

            SET balance= balance + @transfer_amount

            WHERE account = @savings_account

 

If these two UPDATE statements are run without any transaction or error handling logic enforced and the first one is successful and the second one fails, then it is going to only withdraw the money from the checking account and the process will stop and return an error to the call process, but the calling process won’t know what actually happened?  Did the transfer successfully take place? 

 

**Alert user there is a problem:**  One thing that I think unnerves a user more than anything is to receive an error that is totally cryptic to them.  And I can’t think of any couple of errors that sounds worse than “a constraint has been violated” or how about the ever popular, “you’ve been chosen as a deadlock victim”.  Sounds pretty impressive and could only end in disaster for the user.  They have no idea whether or not what they just did became permanent in the database or not.  With providing error logic in cases like this will give the user some idea of what just happened and what they need to do to correct the situation.  They would probably much rather see something like, “The customer you are trying to add already exists” or “System error – please resubmit your change”.  These are more descriptive and give the user answer to what happened and to what their next steps are going to be in order to correct the situation.

 

**Handle unexpected results: ** It might be difficult to plan for every error that comes up, but we can at least trap the unexpected and try to provide additional information about what was happening as the time of the error.  Additional information may include things like how many rows were affected, did the connection fail, was the previous transaction successful, etc… That’s all I have to say about that!

 

**Helps provide data consistency: ** Data consistency is part of what is called the ACID test.  By using error handling in conjunction with transactions, whether they are implicit or explicit, it can help to determine whether or not the data being manipulated is consistent or not.  Let’s brake apart the acronym ACID first.  A = Atomicity, C = Consistency, I = Isolation and D = Durability.  We are taking about the Consistency in this case.  What does it mean to be consistent?  The transaction must provide valid data to be written to the database.  You seen what happened in the above example with the ATM transaction, if the user doesn’t understand that there was an error and what that error was how can they be assured that the data lives in the database as a correct (consistent) state? 

 

There are a lot of constructs in SQL that help to assist us with handing errors.  They range from the following…

 

     @@Error global variable

     RETURN statement

     User-defined error messages

     RAISERROR command

     TRY/CATCH blocks

     XACT_ABORT

 

To see more details on these error handling techniques you can catch my new presentation on error handling titled “We ain't affraid of no errors”, at the St. Louis SQL Server User Group meeting on June 14<sup>th</sup>, 2011 and after the presentation my slide deck will be available at [http://www.stlssug.org][1] Hope to see you there!

 

 [1]: http://www.stlssug.org/