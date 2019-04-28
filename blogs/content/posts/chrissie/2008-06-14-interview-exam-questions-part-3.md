---
title: Interview/Exam questions part 3
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-14T08:11:45+00:00
ID: 60
url: /index.php/desktopdev/mstech/interview-exam-questions-part-3/
views:
  - 1424
categories:
  - Microsoft Technologies

---
[Read the previous post.][1]

So lets move on to question number 5.

The question was &#8220;**If you have a collection of Time elements what do you have to do to put SportsTime elements in it.**&#8220;

Of course the answer is nothing because we used inheritance we can have SportsTime elements in a Time collection. It&#8217;s called _polymorphism_ and it&#8217;s one of the strong points of _OOP_.

Question number 6

&#8220;**I have a collection of Time which also contains SportsTime elements. I want to execute the method ToUniversalTime on it. Will I always get the same result. I yes elaborate and explain what you have to do to get the correct result. If No eleaborate.**&#8220;

Yes you will always get the same result for all the objects in that collection even the SportsTime ones. If you wan to get the SportsTime specific implementation you will have to cast the result to SportsTime. For example in C# this would be.

```
List&lt;Time&gt; times = new List&lt;Time&gt;();
Time time1 = new Time(12,12,12);
Time time2 = new Time(12,12,12);
SportsTime sportstime1 = new Time(12,12,12);
times.Add(time1);
times.Add(time2);
times.Add(sportstime1);
// To get the ToUnversalTime of Time do nothing
Console.WriteLine(times(2).ToUniversalTime());
// To get the ToUniversalTime of SportsTime cast
Console.WriteLine(((SportsTime)times(2)).ToUniversalTime());```

 [1]: /index.php/DesktopDev/MSTech/interview-exam-questions-part-2