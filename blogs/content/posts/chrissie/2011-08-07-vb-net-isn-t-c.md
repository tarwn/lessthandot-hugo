---
title: 'VB.Net isn’t C#'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-07T04:29:00+00:00
ID: 1290
excerpt: |
  I've liked and used VB.Net since 2003 and I used C# since about 2005. Me and VB.Net have had a love-hate relationship since the beginning. 
  
  I love it's verbosity and expressiveness I just love the syntax. And it just does everything I need it to do f&hellip;
url: /index.php/desktopdev/mstech/vb-net-isn-t-c/
views:
  - 18933
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - vb.net

---
I&#8217;ve liked and used VB.Net since 2003 and I used C# since about 2005. Me and VB.Net have had a love-hate relationship since the beginning. 

I love it&#8217;s verbosity and expressiveness I just love the syntax. And it just does everything I need it to do for the applications I have to write. I don&#8217;t see a reason to leave it behind for something new and different. 

I hate how they sometimes screw up the syntax, yes lambdas look ugly. The whole version 9 debacle where they implemented lambdas in a half-assed way. 

And then there has been all the debates about &#8220;is VB.Net just as good as C#&#8221;. And no it isn&#8217;t. It&#8217;s different. 

You can not assume that the 2 will compile to the same IL and thus you can not assume that they will compile to the same machine-code. You can not assume that what you wrote in C# will be usable by a VB.Net assembly. And you can&#8217;t assume that the opposite will happen. 

There are to many little difference that will bite you if you assume they are the same and that they are compatible. 

Oh sure, they are 95% the same, but it&#8217;s the 5% that matters in the real world that will hurt you. It&#8217;s that 5% that will make you cry like a baby, because you will have to write hacks to fix it. And you will have to write a unit test in each language just to be sure your assumptions are correct. 

It is sad to see that most people will say that it is ok to pick VB.Net over C# because they are just the same and they use the same framework, and it&#8217;s even true. But they are only telling you half of the story. 

It&#8217; s like saying a Lamborghini and a Ferrari are both cars so if your mechanic knows how the fix one he will know how to fix the other. Which of course is true 95% of the time. but no 100% of the time. Because they are different.

There is no shame in being different. There is no shame in choosing one over the other. But you have to know that if you do choose that there is a difference. 

So it frustrates me to no end that people answer VB.Net questions by VB.Net users with a &#8220;this is how it works in C# and it should be the same in VB.Net&#8221;. Those are not helping anyone and they show a lack of respect. Because the difference is there. 

I have blogged about the many difference here in the past. and some of them are really stupid.

  * [Some more string concatenation weirdness this time with Enums in VB.Net and a difference with C#][1]
  * [Why you should never concatenate string with + in VB.Net and how resharper can help.][2]
  * [fluent security and making it work in VB.Net][3]
  * [impromptu-interface instead of clay for VB.Net][4]
  * [Handling of events a small difference between C# and VB.Net][5]
  * [VB.Net and the past: The return thing and C#][6]
  * [Automatic properties and their backing fields big difference between C# and VB.Net][7]
  * [&#8230;][8]

So I wish people would stop answering VB.Net questions just because they know C#. You might just be giving the wrong advice.

 [1]: /index.php/All/?p=1373
 [2]: /index.php/All/?p=1372
 [3]: /index.php/All/?p=1347
 [4]: /index.php/All/?p=1318
 [5]: /index.php/All/?p=1280
 [6]: /index.php/All/?p=1279
 [7]: /index.php/All/?p=1272
 [8]: /index.php/All/?disp=authdir&author=7