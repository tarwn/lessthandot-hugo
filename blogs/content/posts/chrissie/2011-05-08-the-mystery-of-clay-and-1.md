---
title: The mystery of Clay and VB.Net a better explanation
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-08T15:07:00+00:00
ID: 1164
excerpt: |
  Introduction
  
  Over the last 2 days I played with Clay because a certain Scott Hanselman posted about it. I found it to work mostly in VB.Net too apart from the anonymous types. That kinda intrigued me and I posted it on Stackoverflow but did not get a&hellip;
url: /index.php/desktopdev/mstech/the-mystery-of-clay-and-1/
views:
  - 1958
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - clay
  - vb.net

---
## Introduction

Over the last 2 days [I played with Clay][1] because a certain [Scott Hanselman][2] [posted about it][3]. I found it to work mostly in VB.Net too apart from the anonymous types. That kinda intrigued me and I posted it on Stackoverflow but did not get a good answer yet. So then I contacted the [VB-team][4] via their blog. And I got a reply from [Lucian Wischik][5] Spec Lead for Visual Basic. 

## The answer

> Hi Christiaan.
> 
> Try this in C# and you’ll get exactly the same error as VB was giving:
> 
> dynamic c = new ClayFactory();
          
> var x = new[] { 1, 2, 3 };
          
> var plant = c.Plant(ref x);
          
> Console.WriteLine(plant.Length);
> 
> But remove the “ref” keyword, and it’ll work…
> 
> WHAT’S GOING ON
> 
> In C#, “ref” parameters are explicitly stated at the callsite (except in the case of COM where the ref keyword can be omitted). In VB, the “ByRef” modifier is only needed on the declaration.
> 
> So what happens in VB late-binding is that it always assumes ByRef parameter, just in case.
> 
> THE ISSUE
> 
> Clay isn’t robust enough to deal with ByRef parameters. This will require a bugfix by Clay. If the C# code I posted can be made to work, then VB will work. In the meantime, I’m afraid I can’t think of any VB workarounds.
> 
> The problem can be found in the Clay source code, ClayMetaObject.cs, BindInvokeMember, the code which you pasted on your blog. It has this code
> 
> var argValues = Expression.NewArrayInit(typeof(object), args.Select(x => Expression.Convert(x.Expression, typeof(Object))));
> 
> which, in the case of a ByRef parameter, just copies the “ByRefness” over into the lambda it generates. This function needs to be modified to remove ByRefness if it was there.
> 
> Personally, I think that expression trees are just difficult to work with correctly. Whenever you write code that consumes expression trees you have to do an enormous amount of testing to make sure you cover every possible input tree. It’s hard for people to do this comprehensively.
> 
> WHAT YOU SAW
> 
> You observed that AnonymousTypes have slightly different codegen between VB and C#. That doesn’t matter in this case – the same issue manifests with named types, or even with arrays as I used above.
> 
> You observed that VB and C# use entirely different mechanisms for doing late-bound invocation. VB simply calls straight into its existing late-binder. C# does a whole lot more, keeping a local “cache” of the most recent dynamic resolution in its method. But those things don’t matter either.
> 
> &#8212;
> 
> Lucian

I appreciate this very much. Let&#8217;s not forget it is Sunday. 

## Conclusion

I guess the problem is with Clay and it&#8217;s implementation. I will try to talk to the Clay team and see if we can resolve this. 

And I want to say thank you to Lucian once again for the quick response.

 [1]: /index.php/DesktopDev/MSTech/using-clay-in-vb-net
 [2]: http://www.hanselman.com/
 [3]: http://www.hanselman.com/blog/NuGetPackageOfTheWeek6DynamicMalleableEnjoyableExpandoObjectsWithClay.aspx
 [4]: http://blogs.msdn.com/b/vbteam/
 [5]: http://blogs.msdn.com/b/lucian/