---
title: SQL Server Licensing for Non-Production
author: Kevin Conan
type: post
date: 2013-02-14T12:25:00+00:00
ID: 1994
excerpt: 'One of my favorite movie quotes comes from Under Siege 2 (slightly altered to avoid cursing):  "Assumption is the mother of all screw ups".'
url: /index.php/datamgmt/dbprogramming/mssqlserver/sql-server-licensing-for-non-1/
views:
  - 14467
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - licensing
  - sql server

---
One of my favorite movie quotes comes from Under Siege 2 (slightly altered to avoid cursing): "Assumption is the mother of all screw ups".

Before we go any further, if you are ever unsure about SQL Server Licensing, contact Microsoft (1-800-426-9400 open Monday through Friday 6:00 AM PST to 6:00 PM PST). What is valid for me at this point in time may not be valid for you or even for myself at a different point in time.

# The Back Story

One of my most recent "egg on my face" moments came from a discussion on twitter about SQL Server licensing in non-production environments. A question came in on the #sqlhelp hashtag asking about a situation where the QA group at their company installed Enterprise Edition and the person wanted to know if they had to uninstall it and reinstall Developer Edition to be compliant with licensing. 

<div class="image_block">
  <a href="http://www.flickr.com/photos/xurble/376588066/"><img alt="court" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/neon question mark.jpg?mtime=1360851095" width="300" height="300" /></a>
</div>

At a previous company that I worked for, I was always told by the Database Manager that if the system was non-production, licensing was free. So I chimed into the twitter conversation and was then challenged by Robert Davis ([T][1]|[B][2]) that it should be verified by a licensing expert. He challenged it based on that Enterprise Agreements can override regular licensing. My previous employer had an Enterprise Agreement and my current one does not.

# 1st Assumption

My first assumption was that the licensing rules that applied to my previous employer would carry over to my current employer. The problem with that assumption is something I already pointed out, one had an Enterprise Agreement and one did not. So I really was not comparing apples to apples.

Now I too had a stake in finding the correct answer this licensing question. So I offered to the conversation to contact the licensing expert at the vendor from which my current employer purchases their licenses from. After a couple of e-mails back and forth we had a mostly clear answer that because the production workflows never touch the non-production SQL Instances, you were indeed covered by the production licenses and did not need extra ones. This turned out to NOT be true.

# 2nd Assumption

My second assumption was that someone who didn't work directly from Microsoft can 100% be trusted with licensing questions. SQL Server Licensing is well known for being complex and very difficult to understand. I do not blame or think less of our software vendor for coming back with the wrong answer because I'm sure my preconceived thoughts on what the right answer is swayed them.

I replied back on the twitter conversation with what I talked about with my vendor. Fortunately Steinar Anderson ([T][3]|[B][4]) challenged me again to go straight to Microsoft to verify what the correct answer is.

# The truth (for my situation)

I called Microsoft's Licensing and was able to get ahold of someone with a couple minutes and they told me that unless you have an Enterprise Agreement, Microsoft does not look at production and non-production differently. You have to license each one. For the non-production SQL Instances you can use Developer Edition but must have a license of it purchased for each person who will use any of the Developer Edition SQL Instances.

So now I will leave you with two of the favorite DBA sayings about Licensing: "It depends" and "Always confirm with Microsoft about your licensing situation".

 [1]: https://twitter.com/SQLSoldier
 [2]: http://www.sqlsoldier.com
 [3]: https://twitter.com/SQLSteinar
 [4]: http://www.sqlservice.se/blogg