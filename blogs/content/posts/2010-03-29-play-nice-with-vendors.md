---
title: Working with vendor-provided databases
author: Ted Krueger (onpnt)
type: post
date: 2010-03-29T10:20:02+00:00
ID: 738
excerpt: 'Don’t be afraid to call your vendor and begin to create a professional relationship with them.  Always work together to build on the systems that are working hard to keep both companies alive.'
url: /index.php/itprofessionals/ethicsit/play-nice-with-vendors/
views:
  - 4774
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
  - IT Service Management
  - Project Management
  - Software and Configuration Management
tags:
  - community
  - help
  - sql server
  - third party databases
  - vendor

---
## Will you be my friend?

<div class="image_block">
  <img src="/wp-content/uploads/blogs/ITProfessionals/friends.gif" alt="" title="" width="134" height="149" align="left" />
</div>

When it comes to database performance issues, finding the cause and the resolution is usually half the battle, if not most of it. But when the database involved is vendor-provided, getting the resolution implemented can actually be another (long) story. Not only does the vendor have to be involved, they also have to somewhat agree to the resolution and participate actively in implementing it. This is the reason it is so important to build a good relationship with your vendor—and to do so **_before_** it’s actually needed.



## Make a professional relationship

How do you do this? The answer to the question is simple and can be done with a few emails or calls over time. This relationship should start as early as the review process before choosing to purchase the software for you business requirements. Interact with the vendors in the demo process to build respect between each other. Even when problems are not present, it can be an asset to keep in touch with key individuals that are handling or working directly on the database structure. You will be able to react more quickly and successfully to performance issues in vendor-provided databases if you already have a good, working relationship with the vendor. This doesn’t necessarily mean calling them up daily and talking to them about your NCAA picks (in fact that may not be a good idea at all). You want to build a technical relationship on a professional level but at the same time build trust and respect for each other. Portray your abilities, concerns, thoughts and understanding of the database the vendor has taken great time in creating. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/ITProfessionals/fishmarket.gif" alt="" title="" width="600" height="450" />
</div>

Think of it as a fish market shopping experience. If you have a good rapport with the fish vendor, you will most likely get the big tuna behind the counter and not the Chinese carp that they are trying to pawn off as a walleye displayed on the shelf. 

## Don’t break the relationship

When the problem does occur, don’t’ be rude. Don’t flame their design. You don’t want to be sent back to the first-level support process. Once you’ve broken the relationship, rebuilding can be in some cases, impossible.
  

  
Here’s a good example. A few weeks ago, a database behind a warehouse management system started to show progressive performance problems. After some troubleshooting, we discovered that forwarded records on HEAP tables were the root cause of the performance problems.
  

  


> Finding forwarded records can be done with the following query written by Jonathan Kehayias ([blog][1] | [Twitter][2])</p> 
> 
> sql
SELECT 
>     OBJECT_NAME(ps.OBJECT_ID) AS TableName,
>     i.name AS IndexName,
>     ps.index_type_desc,
>     ps.page_count,
>     ps.avg_fragmentation_in_percent,
>     ps.forwarded_record_count
> FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL, NULL, 'DETAILED') AS ps
> INNER JOIN sys.indexes AS i 
>     ON ps.OBJECT_ID = i.OBJECT_ID 
>         AND ps.index_id = i.index_id
> WHERE ps.forwarded_record_count > 0
```

To learn more about forwarded records and HEAP issues, check Sankar Reddy’s ([blog][3] | [Twitter][4]) blog, “[How can I tell if a SQL Server system is affected by Forwarded records?”][5] and also a very good [reply][6] from Jonathan Kehayias ([blog][1] | [Twitter][2]) in the msdn forums [here][6].
  
Once the problem was identified as forwarded records on a HEAP table, it was found that the table had a unique non-clustered index. This left the opportunity to resolve the forwarded records issue by converting the unique non-clustered index into a clustered index and would resolve the problems.
  

  
Even knowing the problem and solution to this problem was quickly identified, the database is still a vendor database so altering the structure surly void all support agreements. However, given the rapport with the vendor that was already established, a well composed email explaining the problem in great detail was all that was needed.
  

  
The email (has been altered to keep to a NDA):

> _Good morning everyone,</p> 
> 
> I have identified a forwarded records issue with the {name removed} database that is causing some performance problems. I’ve narrowed the problem to two HEAP tables listed below
  
> Table1
  
> Table2
  
> Both tables do not have a clustered index but have unique non-clustered indexes. Is there specific reason behind the unique non-clustered index over a clustered? I was thinking it may benefit the performance to replace the non-clustered index with a clustered index. This will remove the HEAP and then possibly fix the performance bottleneck of the forwarded records?
> 
> The forwarded records count is very high on both tables so I would like to see if the index change can be performed without affecting the structure and functionality of the entire process these tables support. Please let me know your thoughts. If you want to jump on a conference call and or WebEx to go over this in detail, I’d more than happy to. Our sessions always turn out with great results and look forward to them.
> 
> Sincerely,
  
> The DBA
  
> </i></blockquote> 
> 
> The reply from the vendor was good. The relationship that was already created made this major alteration to the tables quick and painless. The vendor immediately discussed the situation with the database architects in their team and came to the conclusion the change was a sound and stable improvement. The change was also added to updates that would be going out so the database changes would be reflected to all of the customers. Because a good relationship already existed between us, the major alteration to the tables was quick and painless. 
> 
> Without the effort put forth to create the good relationship with the vendor, this task could have been a long and drawn out process. Worse, the performance problem could have remained on the database and any workaround fix (from us) would have been at best, temporary. 
> 
> ## What's next?
> 
> Call your vendor and begin to create a professional relationship with them. Introduce yourself. Send them an email.

 [1]: http://sqlblog.com/blogs/jonathan_kehayias/
 [2]: http://twitter.com/sqlsarg
 [3]: http://sankarreddy.com/
 [4]: http://twitter.com/SankarReddy13
 [5]: http://sankarreddy.com/2010/03/how-can-i-tell-if-a-sql-server-system-is-affected-by-forwarded-records/
 [6]: http://social.msdn.microsoft.com/Forums/en/sqldatabaseengine/thread/972f68d2-bbc2-4f89-8757-fe1bc7295a3a