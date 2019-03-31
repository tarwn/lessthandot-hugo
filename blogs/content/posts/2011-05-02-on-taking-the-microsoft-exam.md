---
title: On Taking the Microsoft Exam 70-448
author: rwaters
type: post
date: 2011-05-02T09:40:00+00:00
excerpt: 'You win some, you lose some.  I just took my first Microsoft exam, the 70-448, that covers the BI suite of Analysis Services, Integration Services and Reporting Services for SQL Server 2008.  Wow.  The test was harder than I expected.  Although plenty o&hellip;'
url: /index.php/webdev/business-intelligence/on-taking-the-microsoft-exam/
views:
  - 5290
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence

---
You win some, you lose some.  I just took my first Microsoft exam, the 70-448, that covers the BI suite of Analysis Services, Integration Services and Reporting Services for SQL Server 2008.  Wow.  The test was harder than I expected.  Although plenty of time was allotted for taking the exam, some questions included material I hadn’t seen before, even though I read Microsoft’s _Self-Paced Training Kit_ for the exam, cover to cover.  No, I didn’t pass.

Let me back up a bit.  Last summer, I took a week-long course on SSAS 2008 through Global Knowledge and it was great.  Although expensive, devoting a whole week to learning a completely new subject was a real treat.  The instructor mentioned the 70-448 exam and I thought, why not take it ?

To prepare for the exam, I picked up Microsoft’s Training Kit at the instructor’s recommendation.  Although getting through it at times was as torturous as some of the Pilates moves I subject myself to on a Saturday morning, I would recommend it.  Regardless of my test results, the book and practice tests that come with it have helped me better understand many key concepts.  Take the different storage modes for SSAS:  MOLAP, HOLAP, ROLAP.  After my SSAS class, I had a general idea of what these terms meant, but it wasn’t until I came across the following questions on the practice exams that the differences among these modes became clear:  “Which storage method is the best for fast querying ?” (MOLAP), “Which storage method is best if you want to maximize space on the SSAS server, but still want aggregations stored in the cube ?” (HOLAP)

Now, about that material I hadn’t seen before…  One of the questions referenced an MDX statement (I won’t name it so I don’t get in trouble) that was never mentioned in the class I took, nor in the Training Kit.  Okay, okay, this statement _is_ explained in the separate, _Microsoft SQL Server 2008 MDX_ book I bought soon after I took the class (when I was really enthusiastic).  This book came in handy when I needed to create a custom calculation for a report that has a cube for a data source, but have I read the entire book ?  Of course not.  I’m afraid if I get lost in MDX, I’ll never find my way out !

For the Integration Services part of the exam, I didn’t take a class.  Yes, you guessed it, I bought another, big, fat, book – _Professional Microsoft SQL Server 2008 Integration Services_ by Wrox.  I haven’t read the whole thing, just picked out what I’ve needed here and there.  I’ve also learned a lot on the subject from my friend and mentor, Ted Krueger ([Blog][1]|[Twitter][2]) .  I’m definitely not an expert here, but I do know SSIS a whole lot better now than I did a year ago, and I actually like it.

As for the Reporting Services piece, I’ve been creating reports for a few years now, so that section should have been a breeze, right ?  Well, the exam covered a lot of configuration-type tasks that were new to me.  Actually, I’ve gotten Reporting Services to run just fine without knowing many of these features.  I did score best on this section, though.

I will retake the exam – after a whole lot more studying.  Now I know these certifications demonstrate not just knowledge, but mastery of a subject area.  And I’m not quite there, yet.

 [1]: http://blogs.ltd.local
 [2]: http://twitter.com/#!/onpnt