---
title: 'SQL is Hard (still): Reviving a stale application'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2018-02-06T13:20:09+00:00
ID: 8853
url: /index.php/datamgmt/dbprogramming/sql-is-hard-still-reviving-a-stale-application/
views:
  - 4008
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
tags:
  - azure
  - sql

---
Several years ago, I [launched SQLisHard][1] to help folks learn SQL. Some folks learn well from books or videos, but others learn best by getting in there and running queries. I monitored and tweaked things for about a year, trying to get the first set of exercises smoothed out. Then, like many folks, I was sidetracked by other projects and work responsibilities.


<div class="wp-caption aligncenter" style="display: inline-block;">
  <a href="http://www.sqlishard.com/" title="Visit SQL is Hard"><img src="http://www.sqlishard.com/Content/Screenshot.png" alt="SQL is Hard screenshot" /></a></p> 
  
  <div class="wp-caption-text">
    SQL is Hard website: Learning SQL through hands one exercises
  </div>
</div>

<p>
  For about $10/month, this little site has kept running and helping folks. I expected to see traffic die off, since I wasn't adding more content. Instead, there's been nearly 7000 folks that have stopped by and successfully completed one or more exercises despite a total lack of marketing.
</p>

<p>
  The hosting is oversized for what it is, most of the technology is not what I would pick today for options, but it just keeps ticking. So I'm dusting off the build pipeline, rebuilding some data analytics so I can see what's going on, and started fashioning a new set of exercises.
</p>

<p>
  Here's how I went about reviving the application.
</p>

<h2>
  Delivering Change: The Build Pipeline
</h2>

<p>
  One of the things that made it easy to tinker with SQLisHard is the build pipeline for delivering updates. Most of the scripts live in the repo, but I hadn't bothered to document it. Luckily, I had an old backup of the original build server VM and was able to recreate the build process on a much newer one.
</p>

<p>
  Why did I bother?
</p>

<p>
  A build pipeline makes delivering changes consistent. This application has 2 separate databases, runs user-entered queries against a real database, and has to provide accurate feedback every time (or risk hurting someone's progression). A pipeline provides both safety and speed. I was experimenting with several different things at the time, so I have rudimentary testing covering all types of situations. By the time the changes roll out to the website and apply changes to 2 separate SQL Server instances, that one button push has run:
</p>

<ul>
  <li>
    Clean nuget + npm installation to ensure consistency
  </li>
  <li>
    MS Build to make sure they work
  </li>
  <li>
    Some unit tests to make sure the code is happy
  </li>
  <li>
    Applied SQL changes to two beta databases to verify updates work and complete quickly
  </li>
  <li>
    Asset minification for faster site loading
  </li>
  <li>
    Deployed the site to a beta web server
  </li>
  <li>
    Run 13 quick UI tests via Chrome Headless to make sure everything plays together
  </li>
</ul>

<p>
  It was a little painful to bring back to life, but infinitely safer then applying changes manually. It's past time for every company to run a pipeline, and I'm more than happy to dive into more details or other build services if folks are interested.
</p>

<h2>
  Visibility into usage
</h2>

<p>
  The 2013 version of SQL is Hard tracked statistics on how many folks were successful or unsuccessful at each step along the way.
</p>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">On the SQLisHard front, it looks like exercises 1.0, 1.2 and 4.0 need refinement, they have the highest error rates <a href="http://t.co/Hv5OfOL10L">pic.twitter.com/Hv5OfOL10L</a></p>&mdash; Eli Weinstock-Herman (@Tarwn) <a href="https://twitter.com/Tarwn/status/345526863428472835?ref_src=twsrc%5Etfw">June 14, 2013</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


<p>
</p>

<p>
  Visibility into the flow from step to step helped me make adjustments and help folks make it all the way through the exercises. This site used a bunch of experimental things, so the data originally lived in a beta Splunk Cloud offering that was discontinued years ago (and replaced by a cloud offering that did not have an API...). Unfortunately, this activity is not tracked well in the database either.
</p>

<p>
  Azure Insights has come a long way, so I decided to switch to that and not get distracted by rewriting the database back-end. With a few lines of JavaScript, I now have live events flowing into pretty charts again:
</p>

<div id="attachment_8855" style="width: 810px" class="wp-caption aligncenter">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/12/SQLisHard_AzureInsights.png" alt="SQL is Hard: Azure Insights" width="800" height="341" class="size-full wp-image-8855" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/12/SQLisHard_AzureInsights.png 800w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/12/SQLisHard_AzureInsights-300x128.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/12/SQLisHard_AzureInsights-768x327.png 768w" sizes="(max-width: 800px) 100vw, 800px" />
  
  <p class="wp-caption-text">
    SQL is Hard: Azure Insights
  </p>
</div>

<p>
  Every query someone tries in SQLisHard is executed against a sample database and returned to the front-end. Once the front-end receives the result, I send an event to Insights to report the Exercise Id and Status:
</p>

```javascript
dataService.exercises.executeQuery(currentQuery.toStatementDTO(limitResults), function (data) {
  
  // ... receive query results, extract completion status, and display ...

  trackEvent('executeQuery', {
      exerciseSet: exercises().id,
      exercise: exercises().currentExercise().id,
      completedSuccessfully: exerciseCompleted
  });
});
```

<p>
  I query the data in Application Insights (https://azure.microsoft.com/en-us/services/application-insights/) to count the number of success/non-success calls have occurred for each exercise:
</p>

```
customEvents
| where timestamp > now(-7d) 
| project exercise = tostring(customDimensions.exercise), success = tostring(customDimensions.completedSuccessfully)
| where notempty(exercise)
| summarize attempts=count() by exercise, success
| order by exercise asc, success desc
| render columnchart
```

<p>
  Where this pays off is when I start making new exercises public. Now I'll have immediate visibility into how folks are progressing and I can try to improve the exercise descriptions to help as many folks get through as I can.
</p>

<h2>
  New Exercises Coming Soon: Aggregation
</h2>

<p>
  The first exercise set focused on some basic building block SELECT statements. We went through some beginning SELECT * statements, listed columns, and column aliases. We performed WHERE statements with equivalence, LIKE, and BETWEEN. Then we added in JOINs with ON statements.
</p>

<p>
  One of the great things about a relational database is the ability to mine across those related datasets for new information. Aggregation plays a big part here, so I've started building an exercise set that looks like this:
</p>

<ul>
  <li>
    COUNT
  </li>
  <li>
    SUM
  </li>
  <li>
    GROUP BY one field
  </li>
  <li>
    GROUP BY multiple fields
  </li>
  <li>
    MIN/MAX
  </li>
  <li>
    AVG/STDEV
  </li>
  <li>
    HAVING
  </li>
  <li>
    ORDER BY
  </li>
  <li>
    Aggregation data from INNER JOIN
  </li>
  <li>
    LEFT JOIN
  </li>
  <li>
    Then possibly some tricks, like SUM(IF/ELSE/END) statements
  </li>
</ul>

<p>
  If this works out, I'll have a test link posted on twitter (@sqlishard) and, after a trial period, will roll it out live.
</p>

<p>
  I can't promise this will usher in a great deal of additions, it's hard to jump in this code base and not immediately start rewriting all the things plus I have a half-dozen other projects I'd like to be working on too. We'll see what happens.
</p>

 [1]: /index.php/datamgmt/dbprogramming/sql-is-hard/ "SQL is Hard launch post"