---
title: 'T-SQL Wednesday #21 – T-SQL That Should Have Been Flushed Down the Toilet'
author: Jes Borland
type: post
date: 2011-08-10T12:18:00+00:00
ID: 1295
excerpt: |
  This month's topic, hosted by Adam Machanic, is "Crap Code". We've all seen it, and we've all written it. Even me.
url: /index.php/datamgmt/dbprogramming/t-sql-wednesday-21-t/
views:
  - 8752
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="http://sqlblog.com/blogs/adam_machanic/archive/2011/08/03/t-sql-tuesday-21-a-day-late-and-totally-full-of-it.aspx"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/TSQLWednesday_thumb_578C7A06.jpg?mtime=1312985179" width="244" height="244" /></a>
</div>

It's T-SQL <del>Tuesday </del>Wednesday again! This month's topic, hosted by Adam Machanic, is "Crap Code". We've all seen it, and we've all written it. Even me. Here's one of my stories. 

**The Code** 

Once upon a time, not so long ago, I didn't know much about T-SQL. I knew the difference between SELECT and DELETE, but the nuances of OVER() and the capabilities of CTEs were unknown to me. At the time, I was splitting my time between help desk calls and writing reports for a small company. The systems were old, but solid. 

Most of my report requests came from the accounting department. The accounting system had a main table: DETAILS. This table had about 20 columns; most of them CHAR of varying lengths. This is where all our financial transaction data was stored. 

As I started writing reports and becoming more familiar with T-SQL, execution time and plans, and our systems, I realized something: a lot of the "summary" reports were very slow. I couldn't figure it out. Weren't these simply aggregated reports, which were summing totals? 

They were not. 

The worst offender was a view, of a view, of a view, of a view, of a view. Each one summed or counted or averaged something different. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/GoldToilet.jpg?mtime=1312985547"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/GoldToilet.jpg?mtime=1312985547" title="It should have been flushed down this" width="198" height="235" /></a>
</div>

Now, I wouldn't be writing this post if I could say, "The moment I realized this, I pulled out my cape, began re-writing all the queries, and saved the day." 

I did not. 

I assumed they had been written that way for a reason, and they could not or should not be fixed. I continued to build reports based on them. I even wrote a couple other views based on them. 

As time passed, and more data was added to the table, the report performance got worse. I knew something had to be done to fix it. At the same time, I was learning new T-SQL tricks. Eventually, I re-wrote these queries with a combination of stored procedures, functions, CTEs, and letting the reports do some of the aggregation. 

Results were still accurate. Performance improved. Users were happier. I still had my sanity, and I'd learned new things. 

**What I Learned, and Am Teaching You**

First, nested views are usually terrible for performance. 

Second, keep learning. Read blogs, read books, and attend presentations. You never know when something new you read or hear will solve a long-standing problem. It is good to have as many tools in your T-SQL tool box as possible. 

Third, **question everything**. Ask "**why?**" If there is a business reason for the decision, it should be explained to you clearly. If it is something another DBA or developer built, they should be able to tell you why and walk you through their reasoning. If there is no business reason, or you're told "just because" or "that's the way it's always been", feel free to challenge it. Come up with a better way to do it. 

We all make mistakes. **The best thing you can do is learn from them.**