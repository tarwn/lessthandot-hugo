---
title: Site is no more after they discover they did not have SQL Server backups
author: SQLDenis
type: post
date: 2009-01-04T11:31:37+00:00
url: /index.php/datamgmt/datadesign/site-is-no-more-after-they-discover-they/
views:
  - 6386
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - availability
  - backups
  - best practice
  - recovery

---
Oops, that sucks [Journalspace][1] is no more.
  
Here is what they have said about this on their [blog][2]

> It was the guy handling the IT (and, yes, the same guy who I caught stealing from the company, and who did a slash-and-burn on some servers on his way out) who made the choice to rely on RAID as the only backup mechanism for the SQL server. He had set up automated backups for the HTTP server which contains the PHP code, but, inscrutibly, had no backup system in place for the SQL data. The ironic thing here is that one of his hobbies was telling everybody how smart he was.
> 
> This doesn&#8217;t excuse what happened, though: I should have taken a better look at what he&#8217;d left behind, and fixed all of the things that needed fixing.

That is just bizarre, to set up backups is one of the easiest things to do in SQL Server and should also be one of the first things you do. You also have to make sure that you test these backups because if you can&#8217;t recover from them then you do NOT have backups
  
Another thing that you should be aware of is that the backup should never ever be in the same location as your server. It should be stored in another location preferably at least 50 miles from the original location. We had our servers in WTC building one and the backups in WTC building 2, you already know how that ended&#8230;&#8230;..

 [1]: http://journalspace.com/this_is_the_way_the_world_ends/not_with_a_bang_but_a_whimper.html
 [2]: http://journalspace.com/blog/