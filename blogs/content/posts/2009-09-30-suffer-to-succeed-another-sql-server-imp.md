---
title: 'Suffer to succeed.  Another SQL Server Import Story'
author: Ted Krueger (onpnt)
type: post
date: 2009-09-30T17:15:41+00:00
ID: 572
url: /index.php/datamgmt/datadesign/suffer-to-succeed-another-sql-server-imp/
views:
  - 4135
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Today I had a request we have all received before. Basically, reverse everything you did on an export job from a database to flat file sources so we can validate pretty much everything. It's a frustrating request. I have to admit that. I wouldn't be human if it wasn't. Really, the data is all there and if we could rely on the user community not making for "interesting data alterations" we could just run these extracts by date ranges over the normal method.

Ok, so now the developer comes back out in me. Frustrating as it is, this is kind of a fun task to work on. We have a few hundred files ranging in sizes from 6K to 1MB which is nothing scary at all. The files are fixed format with a transaction ID in the first row. The gears start turning... BCP, BULK INSERT with a format files? Well, I had to test those out and to be up front, I failed miserably in them. The transaction ID in that test file killed my FIRSTROW declaration. It would do exactly as my good friend George said after we talked about it. Validate that format file to the rows and throw that first transaction ID smashed in there with the second row. What does that do? Well it imports. Actually imports very fast but you essentially skip the first two rows over the first row with that "smash data session". 

What we have here is a task that is at most, run a few times a year on request. Speed isn't truly a concern. Yes, I said that and it hurt way down deep but it's true. Sometimes you have to take a step to the side of performance to get a job done. Remember we are part of the business solution and there is a reason for cutting corners on some aspects of business. It gets you ahead in the long run! Well, sometimes it does. 😉

So my real developer hat went on at this point. Simple imports failed to do what I needed all around. I was on an hour time constraint to get this out the door and I had nothing. So I started thinking high level. What do we really need to do here? Import every row in a bunch of files that are fixed format. Hey, I did that once or twice. Usually most of us at one time have either scripted files into SQL Server or written hacked up executables with one big ugly button that says, "RUN ME!!!!!!"

Come on, you know who you are. I'll show you mine if you show me yours

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//runme.gif" alt="" title="" width="527" height="191" />
</div>

So after I failed at my normal import practices and knowing a long day of SSIS would go out of my time bounds, I grabbed a new project (almost left WindowsApplication1 as the name :P) and started thinking and the code came out of me as it once did in my earlier programming career. I wrote that small executable in about an hour. Actually, I wrote a little before dinner and the rest after so not quite an hour. The program even logged each line processed, any errors encountered and any transaction ID found. I went as far as helping my validation process of this import by inserting fully qualified file names, line count in the files and date time values so I could write this nasty to validate against the actual files themselves afterwards...

```sql
select 
	DateDiff(s,Min(processdate),max(processdate)) [Total Import Time]
	,count(distinct filename) filecount
	,count(distinct process_line) linecount
from ImportLog (nolock)
where centerlocation = 1
Union All
select 
	DateDiff(s,Min(processdate),max(processdate)) [Total Import Time]
	,count(distinct filename) filecount
	,count(distinct process_line) linecount
from ImportLog (nolock)
where centerlocation = 2
```

Really there is a point to my ramblings. Even knowing the results of that query showed these horrible performance results,
  
(Yes, that is in seconds on import time for such a small amount of processing)

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//speedtoslow.gif" alt="" title="" width="283" height="104" />
</div>

I managed to write the validation report the business wanted in minutes. Now if I would have thought of just getting the job done in the first place and listened to the voice in my head telling me about those first lines in the files, I would have delivered hours after the request. So the parameters I needed to think about before I started working on anything were pretty basic.

## _Parameters to picking the path..._

• The task itself
  
• The automation factor
  
• The ASAP factor

The task itself should always be thought of first. It should tell you the options you have and with experience, you will find the easy path to narrowing the list of options quickly to the right one. Automation is an easy guess. Do I need to ever touch it again? I always come to a task in the sense it will be used more than once. Even when they say it will be throw away code, I keep automating it or making it easy to run in mind. Full automation is the step you have to decide on vs. a "RUN ME" button convenience. Ah...the ASAP factor. We need this ASAP!!!!!! I love that one. I have many meanings of that abbreviation. I can't put them here. I have to keep it clean 😉 

If I would have taken this all into account in the first place, the task would have been quick and painless. 

I do this often. My knowledge of how many ways I can do certain things on SQL Server actually gets in the way in some cases. It's a lesson you can learn but one you can never remove from the nature of your daily functions. In all, this is what makes certain people succeed and keep that quest to better themselves. 

So next time you think it MUST be the fastest way, stop for a second to think if it might actually be better the slow but fast development way. You might be surprised how well you do in the long run.

<span class="MT_red">And the final note: If I find the developers I work with not striving to make their code fast just because I said slow down, I'm deleting all your logins 😉</span>