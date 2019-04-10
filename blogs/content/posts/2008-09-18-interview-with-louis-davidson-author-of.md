---
title: Interview With Louis Davidson Author of Pro SQL Server 2008 Relational Database Design and Implementation
author: SQLDenis
type: post
date: 2008-09-18T22:23:24+00:00
ID: 145
excerpt: 'Pro SQL Server 2008 Relational Database Design and Implementation is one of those books that should be in the hands of every SQL Server developer. There are tons of SQL Server programming books around but none of them covers the fundamentals of a good S&hellip;'
url: /index.php/datamgmt/datadesign/interview-with-louis-davidson-author-of/
views:
  - 12599
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - book
  - data modeling
  - database design
  - interview
  - sql server 2008

---
[Pro SQL Server 2008 Relational Database Design and Implementation][1] is one of those books that should be in the hands of every SQL Server developer. There are tons of SQL Server programming books around but none of them covers the fundamentals of a good SQL Server database, the design. If your design is 'broken' then it is a lot harder to fix it down the road, design is probably the number one reason people have all kinds of problems with their databases. 

I have both the 2000 and 2005 edition of the Pro SQL Server 2008 Relational Database Design and Implementation so I was very excited when I noticed that Louis had finished his latest version. I contacted him to see if he would be willing to do an interview and as you can see he did. Make sure to read the whole interview because there is a way to win a copy.

**Is the book geared towards a beginner/intermediate level user or do you have to be an advanced user to really utilize the information in this book?**

Well, yes and no. Certainly not “beginner” in the completely uninitiated sense at all. Like I wouldn't give my wife or daughter the book and say “go for it.” (Neither of them being technically oriented enough to completely grasp my Logitech remote control at times.) However, a beginner to database programming/design would not be a terrible thing, and I think that they could really get a quick feel for the theory. I don't cover T-SQL much at all, pretty much limited to writing triggers and some architecture advice.

I set the book up much like a college level course would be set up. Theory first, then implementation, and finally a touch of programming. Of course, in college you are sort of a captive audience, so in some ways I wonder who might actually read the first chapter(s) of the book (if I could find out, I have a job offer for them though!) Once I finally get over the whole process of writing the book, I hope to do some polls/focus groups on the book and see how people feel about it as I need to do some trimming for the next version as I expanded so much with the example code this time (good thing, but if the book grows larger so will the price.) Every time I write a new edition, I think to myself “this is the last time” and I make my wife promise to hit me with a baseball bat if I start again, but when I find out that people are actually reading it, well it gets me interested again…and my wife wouldn't hit me with a bat unless I did something far more…well…I digress.

**What are some of the common mistakes you see when people are designing databases?**

Well, not designing their databases would certainly be tops on the list. So much of what I see wrong on the forums is where people are just going with what they know from writing C# or VB, often just treating tables like arrays. The problem with this is that relational coding is NOT the same as functional coding, and the database is the central most piece of almost every system. Screw that up and nothing is ever going to be quite right in that system. In the end, the data is definitely all that the users care about, so getting it right it so important.

Part of the reason I include theory in my book is to help people understand why you need to program a relational database using relation programming concepts. Many people talk about normalization like it is some sort of ancient method for making the database more difficult to work with. The fact is, the SQL language was and is built with normalization in mind. It is very hard to disassemble parts of your structures, either in a row or in a column, but the opposite is very easy. Two columns can be concatenated to form one column. Two rows can be joined to produce a single row. 

I know that I often come off as a zealot, but frankly once you understand normalization, and do it a few times, everything else seems horribly wrong. The thing I find most bewildering is that people feel they can “improve” upon relational theory without even understanding it in the first place. I am a fan of starting every chapter with a quote, and my quote I use for this situation is from C.S.Lewis: “There are no variations except for those who know a norm, and no subtleties for those who have not grasped the obvious.” If you don't know the current agreed upon right way, you cannot argue against it and make changes. So understanding theory first is the first thing to do.

**Do you think every database should have a sequence table?**

Pretty much, yeah. In my new book, I dedicate quite a few pages to the concept of sequence tables and calendar tables. Both of these give you the ability to do some things using relational methods that were freaking ugly using T-SQL's string handling and date functions. I also included some example code that does the sum of two cubes, and finds values where a value is the sum of two cubes in 2 or 3 different ways. Fun stuff. Not terribly practical, but workable and a good thought experiment. One thing I love about writing (and answering forum questions) is that it allows you to break away from “normality” and just enjoy solving problems that have no tomorrow (at least not for you.)

I try to include a utility and a tools schema in every database I design that has dba utilities (like row count history for example) and end user tools (the sequence table and a calendar table) that can be customized to meet the needs of the users/programmers, like if you want to have string manipulation functions, including CLR objects that might get created.

Which brings up the question…Should T-SQL have better math/looping/string handling capabilities? Wait, you are interviewing me, I don't have to answer that one, whew.

**People are using triggers instead of constraints for data integrity; do you think that triggers are abused or maybe even misunderstood?** 

Abused AND misunderstood. Not that triggers aren't (in my at least somewhat humble opinion) very useful, but too often it seems that they are used in bad ways. I don’t know how many times I have seen trigger code that ignores the fact that more than one row can be modified, but it is more times than I have seen triggers written assuming sets of data could change (well, at least when I wasn't writing them!)

I am also writing a section in an upcoming book that the MVP's are producing about SQL Server that covers succinctly the different tools that SQL Server has to offer to protect data. Triggers are near the bottom of the list, just before stored procedures and certainly after any declarative constraints like foreign keys, check constraints, and defaults. That section will be around 15 pages, while in my design book it is around 66 pages…so far less code examples, but that is the way it goes. I wouldn't want to take away from the other amazing authors on that project (nor would I want you to get all the good stuff from my book, he says just a little bit selfishly)

**Finally we have date and time data types, will this be of any help in designing databases and querying against those columns?**

The date data type is probably my favorite new feature in SQL Server 2008. This is the one feature that will eventually change SQL Server designs for the better. It certainly will make working with date data easier. Never needing to store time data when you don't need time data is going to change the world. 

The crazy date values and roundoff issues with all of the SQL Server 2005 and earlier types were just a pain, especially with datetime. The fact that times of '23:59:59.999' rounded off to the next day confused everyone at one time or another. 

The only thing I wish they had added was a time type that didn't just hold a time of day, but held a time duration. Time as it is will be somewhat useful, but it certainly won't be as earth shatteringly useful as the date type. 

**Why do you think people have concurrency issue, is it mostly design or bad code?**

Well, it certainly can be design, but too often it is just basically a matter of tuning. Now here is where things can get bogged down in terminology. A well designed database, in my opinion, doesn't include indexes. This would even be true if SQL Server implemented unique constraints without using indexes (which could certainly be possible.) To me, the database design is complete when data is protected via design and constraint (and even triggers) from poor data. Performance is another level of problem…implementation if you will.

The next thing I do is to start accessing the data (generally in a moderately controlled development environment), and this access is where you we start to tune access. During the development process we start to create data and tune access. Then you do testing for performance and realize what paths the data is accessed in. By this point it seems that 80-90% of the indexes have been found and any changes to the design can be made, for performance' sake. Once real users are involved I usually find the other indexes that need to be added, as well as adjusting the concurrency level as needed. Some change to the design can be necessary, but more often than not it seems that good code plus good indexing is all that is necessary for good concurrency.

My favorite new feature in SQL Server 2005 was the READ\_COMMITTED\_SNAPSHOT setting you could apply to the database. Where snapshot isolation level allows the transaction to see how the data looked at the start of the transaction, READ\_COMMITTED\_SNAPSHOT allowed this to occur at a statement level only, giving you pretty much the same effect as read committed, but with not locking or blocking on reads. For “canned” systems where you can't make major changes to the structures, particularly where they use an optimistic locking method (or no concurrency control in code) that it can greatly improve concurrency.

So, while it certainly can be a design issue, it seems to me that concurrency is more of a tuning effort than anything. A good design can have poor concurrency, as can a bad one. Of course, the good design is easier to tune, no doubt about it. 

**What are the most important things a person can do to master SQL?**

Use it. I mean really, that is the key. My advice is to look yourself in the eye and say to yourself: “I will not use a cursor. I will not use a temp table. I will not use a function.” Then follow that advice until you just can't figure out how not to. I was at one time the king of the cursor, even before it was implemented in SQL Server as a first class citizen. (Hint, we used a temp table and set rowcount 1, get a row, delete that row, do our business, then get the next row.) 

Then, as I was getting decent at SQL, I went to a class on SQL taught by Dr. David Rozenshtein and he really pounded into our heads about the concepts of relational set-based programming. Paraphrasing, but he basically drilled it into our heads to do all that you can in one statement and let the engine do its thing. This isn't always the case, but it is a good way to start. I also loved his excellent book “The Essence of SQL : A Guide to Learning Most of SQL in the Least Amount of Time” and lots of practice and I was a fan. Of course I was pretty much unable to do any functional programming after that (I was a VB fan in the early days…even wrote a compiler for a senior project in college in VB 1.0. Got a good grad, but the professor thought I was nuts).

**Can you list any third party tools that you find useful to have as a SQL Server developer/admin?**

Well, first is a data modeling tool. I use ERwin from CA, but there are several others out there. I know some architects that do things directly via scripts, but I am very visual (I have very little memory for details, regularly looking things up in books, often even my own, one of the reasons I like writing!) and really need the overview and relationship information from the graphical part of the data model. I like to work with the same tool to generate most of the DDL for my tables, constraints, etc, rather than maintaining a script.

The second important tool that I think a designer can have is a database comparison tool. The fact is, no matter how good of a process you might have, changes always seem to get made to the different environments during the development and tuning process. Add to that that sometimes the implementer misses something in the process from dev to production (with stops along the way for testing, tuning, and qa) it certainly isn't a bad idea. I also highly recommend that when working with 3rd party databases that you compare the database before and after any service packs, so you know what is done. You don't want them to drop your customizations (like indexes) and all of the sudden your system runs like retired baseball catcher.

Part of my design process is to use my modeling tool to generate an “ideal” version of a database and then compare it to the last version, building a script for applying to upgrade a database, starting with the compare tool's script, and adding in the other stuff that might need done (like loading a table.)

Next most important to me is a good code formatter. People tend to format their code in really weird ways, sometimes just that I don't like, and sometimes in ways that makes it simply obvious why their code didn't work in the first place.

Since I mentioned Erwin, I will mention that I am also a big fan of the RedGate tools for sure. (full disclosure, I am a member of their “Friends of Redgate” http://www.red-gate.com/About/community\_relations/friends\_of_RG.htm program,) and use them quite often. 

**Name your three favorite new things in SQL Server 2008**

Date data type for sure. I mentioned that a few questions back, so I won't go into that.

Table constructors are pretty nice. Because they take up less space, I probably saved 2 or 3 pages in my book of useless UNION ALL clauses along.

And, even though they aren't quite as wonderful as I would have hoped, the new dependency dynamic management views are pretty nice. It was kind of weird. They were working pretty cool until RC0, then they changed how they worked and errors started to be raised in places that makes them awkward to use. Not impossible, but not as nice as you would hope. Even still, you can use these views in your releases to see if all objects that are needed (less dynamic calls, of course) actually exist. 

The problem with this question for me is that I had high hopes for SQL Server 2008, and quite often they really weren't met (in Microsoft's defense, I wasn't actually led to believe things would happen, just hopes.) For example, the table constructors. If fully implemented the horrible UPDATE syntax with JOINs could finally be retired and replaced by a syntax that protects you from updating the same row to different values in the same statement. Oh well, maybe next time?

I don't want to make it sound like I am not pleased with 2008. I am, and would suggest upgrading as soon as you could. There are so many cool new features like Resource Governor, Auditing, Transparent Data Encryption, MERGE, etc that make it a wonderful next step in the process. It just feels like the core T-SQL components could have progressed a little further. I mean it is SQL Server after all.

**Where can we expect to see you in 2008/2009? Any conferences, seminars or trade shows perhaps?**

My house in front of my new HDTV, for sure. And there are two hot fried chicken places in Nashville that I just can't get enough of…Any readers want to go have lunch with me, we will go to either of them pretty much anytime you want. 

To answer your actual question, I just spoke with Paul Nielsen at the devlink conference in Nashville (www.devlink.net) which is just a fabulous time. John Kellar does an amazing job. Plan to be back there next year.

Next up for me is speaking at the Nashville SQL Server Users' group (http://nashville.sqlpass.org/) on September 26th of 2008. My publisher is sponsoring and I am trying to arrange food that is non-pizza. Following that I will be at the PASS conference (www.sqlpass.org) in November, again speaking with Paul Nielsen on database design, as well as a session on Tuning with Dynamic Management Views.

I am always willing to go more places, but I am just an average moderately average speaker at this point. I am more comfortable sitting here at my desk in super media heaven (I have two computers, three monitors, my TV, an amp and a digital frame all in front of me at my desk.) When I am writing I have myself to edit me at least a few times. As an example, I really don't like meetings where people are throwing around ideas, especially when there is an adversarial air to things. When writing I can craft a response, do research, and not end up agreeing to whatever is said. Too often meetings are won by the charismatic, not the most knowledgeable. No one is really charismatic on paper, it is just words. Of course, the hardest part is getting people to actually read emails. Or long interviews, I reckon.

 **One way to make a person read the whole book is to include Easter eggs and then have a competition, any hidden things like that this time?**

I did this last time and it went over like a lead zeppelin. In fact, I will make it easy for one of your readers. A free copy of my Pro SQL Server 2008 Relational Database Design and Implementation (I love that title. Next title will be so big that it takes two volumes just for the title alone.) book to the first two persons who email Louis@drsql.org telling me where the hidden message was in my 2005 book. 

Thanks to Louis for this interview, if you wonder what to give that DBA/SQL Developer this Holiday Season then you can't go wrong with [Pro SQL Server 2008 Relational Database Design and Implementation][1]

Download chapter 1 here: http://www.apress.com/book/downloadfile/4090

 [1]: http://www.amazon.com/gp/product/143020866X/002-3562882-4309664?ie=UTF8&tag=sql08-20&linkCode=xm2&camp=1789&creativeASIN=143020866X