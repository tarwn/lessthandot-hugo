---
title: 'T-SQL Tuesday #38 – Resolving an SSIS package performance problem'
author: Koen Verbeeck
type: post
date: 2013-01-08T05:04:00+00:00
ID: 1903
excerpt: 'This is my contribution to T-SQL Tuesday #38: Standing Firm. I describe how I resolved an SSIS package performance issue.'
url: /index.php/datamgmt/dbprogramming/mssqlserver/t-sql-tuesday-38-resolving/
views:
  - 10958
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - SSIS
tags:
  - performance tuning
  - ssis
  - t-sql

---
<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US"> </span>
</p>

<div class="image_block" style="text-align: justify;">
  <div class="image_block" style="text-align: justify;">
    <a href="http://jasonbrimhall.info/2013/01/02/t-sql-tuesday-38-standing-firm/"><img style="float: left;" src="/wp-content/uploads/users/koenverbeeck/TSQL2sday37/TSQL2sday.PNG?mtime=1355209029" alt="" width="133" height="134" /></a>
  </div>
  
  <p>
    It's this time of the month again: the T-SQL Tuesday is back and Jason Brimhall (<a href="http://jasonbrimhall.info/">blog</a> | <a href="https://twitter.com/sqlrnnr">twitter</a>) is hosting the 38<sup>th</sup> installment. The theme is <em>"standing firm"</em>, which basically means to tell a story about one of these words: <em>resolve, resolution</em> or <em>resolute</em>. My contribution will be a story on how I once <strong>resolved<em> </em></strong>an issue with an SSIS package taking more than one hour to complete.</div> 
    
    <p>
      <span style="text-align: justify;">At a client, they had a package developed some time ago and after a while it started to slow down enormously, to the point where it took over one hour to complete, if it completed at all. Sometimes out-of-memory exceptions where thrown and they had to start all over again. They asked if I could take a look at the package and perhaps speed it up a little. The package was not a typical data warehouse scenario, but ran on a normalized database which was being used as an OLTP application database and a reporting database at the same time (don't get me started on the design). The package itself was pretty complex – especially the data flow – and it managed quite a workflow. The data set being handled was large, but not that large to be a gigantic problem.</span>
    </p>
    
    <p class="MsoNormal" style="text-align: justify;">
      <span lang="EN-US">I took a quick look on the package and some things immediately popped out:</span>
    </p>
    
    <ul style="margin-left: 20pt; list-style-position: outside;">
      <li>
        <em><span lang="EN-US">Lookup components </span></em><span style="text-align: justify; text-indent: -18pt;" lang="EN-US">There were quite a few of them and some of those had either partial or no caching and even worse, they all used the dropdown box to select the table. This is a big red flag.</span>
      </li>
      <li>
        <em><span lang="EN-US">OLE DB Commands</span></em><span style="text-indent: -18pt;" lang="EN-US"> I just hate those pesky little buggers. They fire off a statement for each row in your data flow, causing an avalanche of transactions against the database. The database in question was on Full Recovery Model, something I had no control over, so logging exploded when this package ran. If you thought the previous bullet is a red flag, this one is Defcon 2.</span>
      </li>
      <li>
        <em><span lang="EN-US">OLE DB Destinations without the Fast Load option </span></em><span style="text-align: justify; text-indent: -18pt;" lang="EN-US">Pretty much the same as an OLE DB command. Each insert is a separate transaction instead of a bulk insert.</span>
      </li>
      <li>
        <em><span lang="EN-US">Redundant or unnecessary logic</span></em><span style="text-align: justify; text-indent: -18pt;" lang="EN-US"> Obviously, this is to be avoided.</span>
      </li>
    </ul>
    
    <p>
      <!--[if !supportLists]-->
    </p>
    
    <p class="MsoNormal" style="text-align: justify;">
      <span lang="EN-US">So how do you tackle a package like this? Let's start with the Lookups. Most of the referenced tables were pretty large, but if you write a query retrieving only the columns you actually need instead of using the dropdown box, you can fit a whole lot more rows in memory. I guess the original creator of the package used partial or no caching because the referenced data sets were too big to fit all in memory. I wrote a few queries selecting only the lookup keys and the columns needing to be retrieved (which is the point of a lookup obviously) and suddenly everything fitted nicely in memory with the Full caching option. Nice.</span>
    </p>
    
    <p class="MsoNormal" style="text-align: justify;">
      <span lang="EN-US">The OLE DB commands. Usually they are used to execute UPDATE statements, as there isn't a way to do set based updates in the data flow. I got rid of them by creating temporary tables in the beginning of the package and inserting the data into the temp table with an OLE DB Destination using the Fast Load option. After the data flow, I use an Execute SQL Task to execute a set-based UPDATE statement by doing an inner join between the destination table and the temp table. You can't believe the performance improvement you get using this technique on large data sets. I'm a strong advocate of using a hybrid combination of SSIS components and T-SQL in SSIS packages. For the time being, updates certainly belong in the T-SQL realm.</span>
    </p>
    
    <p class="MsoNormal" style="text-align: justify;">
      <span lang="EN-US">The OLE DB Destinations without the Fast Load option. No idea why the default was changed to a worse option, but it was quickly resolved: simply change it to the fast load option and you're good to go.</span>
    </p>
    
    <p class="MsoNormal" style="text-align: justify;">
      <span lang="EN-US">Finally I looked at the logic of the package itself. There were some script components doing pretty basic stuff (I guess the creator was a .NET developer doing his first SSIS development) so I removed them and moved all the logic to the source query. I also removed columns that weren't used in the data flow. In SSIS, it's very important to get your row size as small as possible in order to get as many rows as possible in one buffer. I also enhanced the buffer size, so I could get rows faster in and out the data flow. To finish everything off, I removed a SORT component used to remove duplicates and used a DISTINCT clause in the source query. A SORT component is a blocking component – DEFCON 1 for large data sets – and is to be avoided at all costs.</span>
    </p>
    
    <p class="MsoNormal" style="text-align: justify;">
      <span lang="EN-US">All this work took one afternoon. I ran the package and behold, it ran in a mere 4 minutes. From over one hour to just a couple of minutes. The sad part is most performance bottlenecks could easily be avoided. By being <em>resolute</em> (see what I did there?) about some very simple SSIS design principles, you can make a big difference in the performance of a package. If you're interested in SSIS performance tuning, start at this magnificent SQLCAT article: <a href="http://sqlcat.com/sqlcat/b/top10lists/archive/2008/10/01/top-10-sql-server-integration-services-best-practices.aspx?PageIndex=2">Top 10 SQL Server Integration Services Best Practices</a>. It's my bible when tuning SSIS packages.</span>
    </p>