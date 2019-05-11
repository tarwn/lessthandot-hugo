---
title: VBCop or project Roslyn
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-28T10:55:00+00:00
ID: 1210
excerpt: 'A few weeks back I saw the presentation that Lucian Wischik made at devdays 2011. In that presentation he talked about VBCop. VBCop being similar to Stylecop but then for VB.Net and not for C#. I already talked about a need for such a product for VB.Net&hellip;'
url: /index.php/desktopdev/mstech/vbcop-or-project-roslyn/
views:
  - 3379
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - project roslyn
  - vb.net
  - vbcop

---
A few weeks back I saw the presentation that [Lucian Wischik][1] made at [devdays 2011][2]. In that presentation he talked about VBCop. VBCop being similar to [Stylecop][3] but then for VB.Net and not for C#. [I already talked about a need for such a product for VB.Net][4] and why it is not available for VB.Net.

I looked it up on Google but I could not find anything. So I sent a mail to the VB-team to ask what happened to that project. 

> Dear VB-team,
  
> In the devdays2010 session of Lucian about language design in C# and VB he mentioned that you guys are working on VBCop (stylecop for VB). Is this project alive and if so can we use it? Kind regards.
  
> Christiaan Baes

And I got a reply from Anthony D. Green &#8211; Program Manager &#8211; VBC# Compilers (Roslyn). Here is some of that reply.

> Hey Christiaan,
> 
> Indeed this project is very much alive. One reason VB style cop hasn&#8217;t been easy to implement in the community outside of the VB team is because in order to do so one must write an analysis engine which understands the entire VB language. As the VB language itself is very syntactically flexible the meaning of things is very tightly tied to semantic analysis. For example in VB whether a set of parenthesis is a method invocation, or an array or collection index depends on a number of factors like overload resolution and the types of expressions involved. When you dig further into the more advances features of the language such as type inference, implicit conversions, and anonymous delegates it becomes simply impossible to analyze a VB program without a very deep understanding of the language and compiler internals. Naturally this is a very daunting task that anyone would understandably shy away from.
> 
> While we do have some tools internally which expose some of this none of them are really robust enough for an open platform on which others can build their own rules. That&#8217;s where the Roslyn project comes in. You may have heard about the Roslyn project from Anders Hejlsberg&#8217;s presentation at PDC. Roslyn is an effort to actually build such an engine with a robust API built directly on top of the internals of the Visual Basic compiler itself. This would allow developers such as yourself access to all of the details mentioned above and more without managing all of the complexity. Unfortunately, the Roslyn project hasn&#8217;t yet released any public bits and as Roslyn would quite sensibly be the foundation of VB StyleCop I don&#8217;t have anything I can offer you right now. <span class="MT_red">We haven&#8217;t announced a timeframe for Roslyn&#8217;s release so I can&#8217;t give you any dates or projections but</span>, yes, it is an active project currently internal to the VB team and it&#8217;s definitely something we&#8217;d like to deliver in the future after Roslyn releases public bits.
> 
> In the meantime, are there any specific rules you or your team is particularly interested in? The list of rules we&#8217;ve considered internally is constantly growing and I&#8217;d be very interested in your ideas!
> 
> Regards,

So something is coming but when is still an open question.

And here are some of the things I would like to see in such a product.

I guess the first rules that come to mind are namespaces being the same as the folder they are in but resharper already checks that for me. Of course variable/method naming is very important. I like to always add the CLSCompliant = true so that also helps. I like to have a one class per file approach check. And I think most other rules that are in Stylecop now, like the comment, layout and spacing rules. Apart from the curly braces rules of course. 

It would also be nice if it would [detect usage of assign to functionname][5] and therefor not using return.
  
Warning me when someone uses [delegate relaxation][6].
  
Warning about the use of [string concatenation][7] would also be nice.

So I&#8217;m hopeful something like this will be added into future versions of VB.Net. 

Do you have any suggestions?

At [Build Anders Hejlsberg also talked about this][8], so it is no secret project.

> In this talk, Technical Fellow Anders Hejlsberg will share project plans for the future directions of C# and Visual Basic, including a discussion of what trends are influencing and shaping the direction of programming languages. Anders will talk about asynchronous programming and Windows 8 programming, coming in the next version of Visual Studio. He will also discuss the long-lead project "Roslyn", including object models for code generation, analysis, and refactoring, and upcoming support for scripting and interactive use of C# and Visual Basic.

 [1]: http://www.wischik.com/lu/
 [2]: http://channel9.msdn.com/Blogs/matthijs/How-do-we-do-Language-Design-at-Microsoft-VB-and-C-by-Lucian-Wischik
 [3]: http://stylecop.codeplex.com/
 [4]: /index.php/DesktopDev/MSTech/i-really-like-stylecop-and-why-it-s-only?highlight=stylecop&sentence=
 [5]: /index.php/DesktopDev/MSTech/vb-net-and-the-past
 [6]: /index.php/DesktopDev/MSTech/handling-of-events-a-small
 [7]: /index.php/All/?p=1292
 [8]: http://channel9.msdn.com/Events/BUILD/BUILD2011/TOOL-816T