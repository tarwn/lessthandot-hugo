---
title: 'T-SQL Tuesday #44 – The Second Chance'
author: Jes Borland
type: post
date: 2013-07-09T12:40:00+00:00
excerpt: This month, our host, Bradley Ball, wants to hear about at least one moment in SQL time that I would like back.
url: /index.php/itprofessionals/professionaldevelopment/t-sql-tuesday-44-the-1/
views:
  - 6646
rp4wp_auto_linked:
  - 1
categories:
  - Professional Development

---
[<img style="float: left;" src="/wp-content/uploads/users/grrlgeek/TSQL2sDay150x150.jpg?mtime=1365451350" alt="" />][1]It&#8217;s the second Tuesday of the month, and you know what that means: it&#8217;s T-SQL Tuesday! This is a monthly blog series with a different topic each month. This month, our host, Bradley Ball ([blog][2] | [twitter][3]), wants to hear about at least one moment in SQL time that I would like back. (You can write about your moment, too – [the information you need is here][1]!)

_Remember that time I didn’t know how to configure a cluster?_

Let me take you back a few years. I was green back then, a recently-graduated-from-technical-college report writer, just promoted off the help desk. I loved SSRS and wrote some decent T-SQL, but one day everything changed. The company’s DBA left for another job, and I became responsible for the databases. Our server administrator was new to the gang, too.

This was the spring that our hardware was being updated, and this server was being upgraded. It was a Windows Server and SQL Server cluster. What did I know about clustering in those days? Looking back on it, not much. Not much at all.

The servers had been ordered and delivered without my input and before the sys admin arrived. When they finally arrived, we got to open the boxes in the server room and install the RAM. It turns out neither of us really knew what to do after that.

We made a game plan. We were going to take a weekend outage. We were going to take the two new servers to the data center. On Friday night, we would take one of the existing cluster servers out. We would replace it with one of the new cluster servers. We would fail over to the new server. We would take the other existing cluster server out. We would replace that with the second new server. We would fail over to it. On Saturday, testing would happen, issues would be fixed, and all would be well.

I’ll give you one guess as to how well that went.

<p style="text-align: center;">
  <a href="http://www.flickr.com/photos/balakov/2468552226/in/set-72157594352657197"><img src="/wp-content/uploads/users/grrlgeek/2468552226_0e2637a0dd.jpg?mtime=1373373473" alt="" /></a>
</p>

<p style="text-align: center;">
  <em>Was this going to be me?</em>
</p>

After planning the outage, driving to the datacenter, and working past midnight, we realized we were unable to add new, non-matching hardware to the existing cluster. I suggested that we shut down all the servers, bring up the new ones, and re-install everything, but the business was not willing to have that lengthy of an outage that weekend. We ended up packing the servers back into the boxes, putting them back on the truck, and going home the next day after performing other required maintenance.

The users didn’t get the promised upgrade and performance improvements, so they weren’t happy. The business didn’t get its investment put in place, so they weren’t happy. The IT manager had to pay overtime and expenses for a weekend without the planned results, so he wasn’t happy.

By the time I sat in my chair on Monday morning, I realized I had a lot to learn. A LOT. This was around the time I discovered the [SQL Server user group closest to me][4], and the SQL community on Twitter. I reached out to a few of the contacts I had and read some blogs, and realized I’d had the right idea. We needed to plan the cluster migration more carefully.

We were never able to get the outage scheduled that summer, and by fall I had left for my first real Junior DBA job. But if I had a second chance, I’d go back and re-do that cluster migration. I’d ask a lot more questions. (It’s hard to believe I’m saying that.) I’d question the sys admin’s plan more. I’d spend more time in the weeks leading up to it reading about clustering, trying to understand it better. If I could do it all over again, I’d be more prepared.

 [1]: http://www.sqlballs.com/2013/07/t-sql-tuesday-44-second-chance.html
 [2]: http://www.sqlballs.com/
 [3]: https://twitter.com/SQLBalls
 [4]: http://www.sqlpass.org/PASSChapters/LocalChapters.aspx