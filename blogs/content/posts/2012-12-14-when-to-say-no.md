---
title: 'SQL Advent 2012 Day 14: When to say no'
author: SQLDenis
type: post
date: 2012-12-14T12:47:00+00:00
excerpt: |
  This is day fourteen of the SQL Advent 2012 series of blog posts. Today we are going to look at when we should say no to your boss or the company you are consulting for.
  
  
  
    
  That is all for day fourteen of the SQL Advent 2012 series, come back to&hellip;
url: /index.php/webdev/business-intelligence/when-to-say-no/
views:
  - 19996
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence

---
This is day fourteen of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at when we should say no to your boss, coworkers or the company you are consulting for. 

[<img alt="Darth Lumberg" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/DarthLumberg.PNG?mtime=1355422704" width="183" height="223" style="float:left;margin:0 5px 0 0;" />][2]A couple of weeks ago someone came to me asking me for some help because he had a syntax error in a query, this person asked me to help him fix this error, something about an error near a single quote. I thought to myself, this is a 3 second fix, I&#8217;ll walk over, fix the query and go back to what I was doing. The fix was very easy, then this person asked me if we could add one column from another table to this query&#8230;.. after all was said and done I actually was there for almost two hours and ended up writing a whole proc for this person. But lesson learned, you can bait and switch me once but not twice, now when this person ask me for something I always ask right away if that all he wants. It is easy to say no to a person like this, saying no to your boss or the business users probably needs more courage. 

I especially &#8216;love&#8217; when a business person tells you that something that they want you to do is pretty simple and should only take you x hours. Stop right there buddy, there is a reason you are the tech person and the business person is not, you might have an idea how long something should take, but even then you are probably going to be off. Estimation is just very hard to do.

## Changing requirements or unclear requirements

[<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/design.PNG?mtime=1355424774" width="240" height="303" style="float:left;margin:0 5px 0 0;" />][3]I once worked with this PhD type person who would come up with the most complex and elaborate reports that had all kind of calculations in them. You would code it exactly to spec, you showed it to this person and he would start changing things left and right. This person thought, if you can change the layout and formulas in Excel in an hour then it should not take you more than an hour either. His favorite thing to say was that _nothing is written in stone_.

On another project the client was shown a design for a website, they agreed, this was pretty well engineered, each section would inherit from a base template. Once we delivered the first version, they started to change each section and in the end all the benefits that they did have from template driven sections went down the toilet, each section was now redone from scratch. How they got us to do this was by doing it slowly over time, first they started with one section, then another, then another. In the end we fired them as a client because they didn&#8217;t want to pay for all the changes that they demanded after the design was approved.

## Say no when you are pressured to deliver at a certain date and you don&#8217;t know if it is feasible

[<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/officespace.PNG?mtime=1355496247" width="366" height="275"  style="float:left;margin:0 5px 0 0;" />][4]This might be very hard to do, if your boss tells you to have this done by Friday you can&#8217;t just say no. However it is unprofessional if you don&#8217;t say no or at least explain why you think it won&#8217;t happen. Some bosses will not take no for an answer and will promise you free lunches, dinners and stays in hotel rooms(if you have a long commute) in order to motivate you. The boss does this because he committed to a date without consulting you. What you can say is that you might be able to finish 90% of it and have the look and feel ready but 10% will be non functional. This might be an acceptable compromise.

I had a boss once who would stop by every hour or so and wanted to know what exactly everyone on the team was working on at that moment. Whenever he walked into the developer area the anxiety level would shoot up, everyone was uncomfortable. This actually was having an impact since people would worry what the boss was thinking and got distracted with that.

In the end there are only a couple of different outcomes:

  * Somehow you finish it all on time but you feel like crap because you have been putting in 100 hour work weeks, you know some of the code has been put together hastily and is not designed as good as you would want to. It&#8217;s probably nott very nice to be around you at this time
  * You miss the deadline and now everyone is pissed
  * You deliver it on time but it is a complete disaster when the client starts using it, things don&#8217;t work as expected, there is lots to be fixed

Now you can do a post-mortem and figure out what went wrong and how to avoid the same outcome next time. Was the date to aggressive, were the requirements not clear, was the analysis incorrect, was the technology stack the problem? If you could go back in time, what would you have done differently?



## Say no when you know it is a waste of time

Sometimes you will be asked to do things which don&#8217;t make sense or are irrelavant to what someone wants to accomplish. Take a look at this question in the [Server Metrics Data Mart][5] forum post

> I have been working on getting a SQL Server data mart in place to gather metrics on our servers and everything in them. I am fairly new to writing complex SQL scripts and am having a hard time figuring out how to gather **how much storage space a particular column in a table is using**. Any help is much appreciated. 

That requirement is a little silly, what if that column is also in 4 indexes and 2 indexed views? Why not going per table instead? If someone asked me this I would question why they need this since it doesn&#8217;t give them the complete picture. What does it matter how much you can store in a column if your fillfactor is 50 and your indexes are completely fragmented? It is your duty to inform the person who asked for this that it is a waste of time and there are better ways to accomplish what they want.

That is all for day fourteen of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][6]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /wp-content/uploads/blogs/DataMgmt/Denis/ADvent/DarthLumberg.PNG?mtime=1355422704
 [3]: /wp-content/uploads/blogs/DataMgmt/Denis/ADvent/design.PNG?mtime=1355424774
 [4]: /wp-content/uploads/blogs/DataMgmt/Denis/ADvent/officespace.PNG?mtime=1355496247
 [5]: http://forum.lessthandot.com/viewtopic.php?f=22&t=17865
 [6]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap