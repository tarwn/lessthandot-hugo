---
title: 'T-SQL Tuesday #54: An Interview Invitation'
author: Koen Verbeeck
type: post
date: 2014-05-13T06:50:59+00:00
ID: 2617
url: /index.php/uncategorized/t-sql-tuesday-52-an-interview-invitation/
views:
  - 8414
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - Professional Development
  - Uncategorized
tags:
  - interview
  - sql server
  - syndicated
  - t-sql tuesday

---
<p style="text-align: justify">
  <a href="http://borishristov.com/blog/t-sql-tuesday-54-interview-invitation/"><img class="alignnone size-full wp-image-2241" alt="TSQL2sday" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/TSQL2sday.png" width="133" height="134" /></a>
</p>

<p style="text-align: justify">
  <span style="line-height: 1.5em">It's time for another T-SQL Tuesday! This month's blog party is hosted by Boris Hristov (</span><a style="line-height: 1.5em" href="http://borishristov.com/blog/">blog</a><span style="line-height: 1.5em"> | </span><a style="line-height: 1.5em" href="https://twitter.com/BorisHristov">twitter</a><span style="line-height: 1.5em">) and the subject is about </span><a style="line-height: 1.5em" href="http://borishristov.com/blog/t-sql-tuesday-54-interview-invitation/">interviews and hiring</a><span style="line-height: 1.5em">.</span>
</p>

<p style="text-align: justify">
  First I thought about writing a rant about the beginning of every interview and hiring process: recruiters. You can't live with them and you can live without them. But I noticed     James Serra (<a href="http://www.jamesserra.com/">blog</a> | <a href="https://twitter.com/JamesSerra">twitter</a>) has already written extensively <a href="http://www.jamesserra.com/archive/2013/06/low-rate-recruiters-the-bane-of-my-existence/">about this subject</a>. Then I considered writing about all of those crazy job adverts that are floating around, but Thomas LaRock (<a href="http://thomaslarock.com/">blog</a> | <a href="https://twitter.com/SQLRockstar">twitter</a>) has already covered this <a href="http://thomaslarock.com/2010/09/a-better-dba-job-description-for-everyone/">nicely</a>.
</p>

<p style="text-align: justify">
  So I kept on thinking and finally decided to brainstorm about interview questions. More specifically, what questions would I ask if I conducted a technical interview with a potential business intelligence developer?
</p>

<p style="text-align: justify">
  First of all, I believe you have to adjust your questions to the expected level of the person being interviewed. If you interview a junior person, I wouldn't start asking how they would implement partition switching in order to speed up the bulk loading of a fact table. If they claim to be senior, you can bring out the big guns after a while. Note I mentioned "after a while". I would start asking basic questions to test their knowledge and then gradually move on to the more difficult stuff. Immediately starting with insane questions to make your conversation partner nervous or uncomfortable is just bad form in my opinion.
</p>

<p style="text-align: justify">
  These are the main subject areas I would test:
</p>

<ul style="text-align: justify">
  <li>
    SSIS (of course). Although some people think of business intelligence just as creating some dashboards and reports, the data has to be there as well. A working knowledge of SSIS is always useful. I'd start with basic questions – what is the data flow and the control flow – and then work myself up to more advanced, broader questions, such as "how would you implement custom logging or restartability in your packages?".
  </li>
  <li>
    Data warehouses. Architectures and modelling, that kind of stuff. I don't expect everyone to recite the differences between Kimball, Inmon and data vaults, but I do expect that there is a basic knowledge about facts, dimensions, star schema versus snowflake and so on (except for very junior people). If a person seems to be well versed in architecting data warehouses, you could go deeper such as for example on the different types of slowly changing dimensions (mini dimensions, outriggers, that kind of stuff. It should provide good discussions) or how to index a data warehouse.
  </li>
  <li>
    T-SQL. Every BI developer that calls himself worthy should know a fair bit of SQL. If it is not for the ETL process, it is for writing reports. Start with the basics (how do you remove duplicates), to more advanced (windowing functions for example). I wouldn't go to deep here (I'm not an expert myself), but I think I would dare to give the interviewee a cursor and ask how they would optimize it.
  </li>
  <li>
    SSAS. I would stay fairly high level on this one. I think you can notice fairly quick if someone knows their way around SSAS or not. I probably would never ask about MDX, DAX or DMX. That would be cruel.
  </li>
  <li>
    SSRS. Again fairly high level questions I think. Maybe because I'm more of an ETL developer and I don't deal with SSRS all the time. I do expect people how to work with shared data sets and shared data sources. A possible scenario to ask in the interview is to give them a slow running report and ask them to optimize it (put filters in the query instead of the report for example).
  </li>
  <li>
    Data Visualizations Tools. Microsoft has a lot of choices: Excel, SSRS, Power View and PerformancePoint. It might be a good idea to propose some scenarios and ask which tool they would use and why.
  </li>
  <li>
    SQL Server itself. It depends on the position, but usually I deal with some light SQL Server administration and development. How to use stored procedures and functions, how to create a database (and which default settings they would change), how to back up a database and so on. Another question would be how they would optimize a database to support bulk loading of data.
  </li>
</ul>

<p style="text-align: justify">
  If I'm in a really bad mood, I could ask about SharePoint integration 🙂 Anyway, questions can test for knowledge and experience, but passion and personality are very important as well. I don't really care if someone can't recite all the protection levels in SSIS, these can easily be found with a search engine.<br /> But in the end, I'll let the interview depend on this crucial question: would you use a bar chart or a pie chart?
</p>