---
title: 'I really like StyleCOp and why it’s only for C#'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-14T08:32:02+00:00
ID: 429
url: /index.php/desktopdev/mstech/i-really-like-stylecop-and-why-it-s-only/
views:
  - 9790
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - stylecop
  - vb.net

---
I really like [StyleCop][1] it is the right thing to do when you work in a team even if you are just a team of one. 

Being a team of one is very nice BTW, I happen to like myself ;-). But I degress.

So Stylecop only works for C# now why is that? Doesn&#8217;t Microsoft care about us VB developers, don&#8217;t they think we should write well formated code. Don&#8217;t they think we should write good code. Do they think we should not write code? Afterall the mouse is your friend.

But no (well I hope not anyway) it is a bit more complicate then that as you can see in [this response][2].

> Hi Branko. To understand why there is no version for VB, it&#8217;s necessary to understand the history of the tool. StyleCop was written as a Microsoft internal tool, to be used by our own internal developers who are coding in C#. StyleCop is not owned by the C# team, but was developed independently. We released StyleCop externally to allow the community to get the same benefit from this tool that we were getting internally at Microsoft.
> 
> Since the release of StyleCop there have been some calls from the community to do a version for VB. Unfortuantely there is a technical limitation currently to this happening. StyleCop requires a managed language service to parse and process the code before it can be analyzed. For C#, a language service was written from scratch to support the tool. But one doesn&#8217;t exist today for VB, and this is a very high bar to entry. However, work is underway at Microsoft to create new, reusable, hostable managed language services both for C# and for VB. This work is still in the early stages, but the plan is to dump the custom C# parser used in StyleCop in favor of the official managed C# language service when it becomes available. At that time, it will probably also become technically feasible to do a version of StyleCop for VB. At that time, there will need to be some evaluation of the demand for a VB version of StyleCop in the community, and it will likely be up to the VB team to determine whether they want to support this.
> 
> Thanks very much for your comments, and I hope this explanation helps you understand why there&#8217;s no VB version of the tool available today.

And that is a very god reason, let&#8217;s hope they fix it in VS2010 and start looking at VB.Net as a more grownup language. Because I&#8217;m sure if they would do that the quality of VB.Net code woul increase.

BTW I also liked the response 

> @jasonall
> 
> Thank you very much for your explanation!
> 
> This is, at least for me (I may have overlooked something), the first time, such a clear statement has been posted. Given the technical limitations, I now understand, that the lack of VB support is not a &#8220;political&#8221; desition, but a fundamental issue, that cannot be easily addressed.
> 
> I am very glad to hear, that there is work on the way, abeit in early stages, that will eventually make a VB version feasible.
> 
> When that time has come, I hope, that the VB-team will do the right thing, and give us the tool! I want to stress the fact, that the average code-quality of VB.Net code &#8220;out in the wild&#8221; is definitely sorely lacking in many respects, and I believe that it is in the best interest of Microsoft, to educate the developpers. Fact is, that a broken application is often wrongly attributed to the OS by its users. The OS is a Microsoft product, hece the blame often is brought to the wrong doorstep, but nevertheless, Microsoft suffers from the folly of &#8220;uneducated&#8221; developpers. We have seen this Million times over, and one of the remedies would surely be the availability of tools for code analysis.
> 
> I myself stll feel the need of such a tool, because I want to do it &#8220;right&#8221;. Adhering to the &#8220;Best Practisies&#8221; (most of wich are available for VB as well, thank god!), is one thing, but an &#8220;external&#8221; audit would really be appreciated. Much more so, because time constraints often lead to sloppy work (&#8220;I&#8217;ll do that for Version 2 because Version 1 shold have been out the day before yesterday&#8230;&#8221;). The results of such an external audit, which StyleCop effectively is, would give me some handy argument towards my bosses, why I should invest the extra hour. We all know, that in the end, the extra hour comes back thousandfold, but tell that to the bosses in a non-software company.
> 
> Also the availability of such tools creates a win-win situation for Microsoft, as well as for the users of the applications: the application works as expected, and Microsoft as the primary software vendor for the platform is praised.
> 
> Sorry for my English, it is not my mother-tongue, but &#8220;when the heart is full, the mouth spills over&#8221;!
> 
> Branko

And let&#8217;s not forget that it&#8217;s not only the language that maked the program but also the tools and especially the one typing the code.

 [1]: http://code.msdn.microsoft.com/sourceanalysis
 [2]: http://code.msdn.microsoft.com/sourceanalysis/Thread/View.aspx?ThreadId=383