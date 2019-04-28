---
title: 'Why you should never concatenate string with + in VB.Net and how  resharper can help.'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-01T09:48:00+00:00
ID: 1285
excerpt: 'There are several ways to concatenate string in VB.Net. Most will use the &amp;-operator but we all know you should use the String.Format method. And sometimes you might by accident use the +-operator. The +-operator is however not such&hellip;'
url: /index.php/desktopdev/mstech/why-you-should-never-concatenate/
views:
  - 6743
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - concatenation
  - resharper
  - string
  - vb.net

---
## Introduction

There are several ways to concatenate string in VB.Net. Most will use the &-operator but [we all know you should use the String.Format method][1]. And sometimes you might by accident use the +-operator. The +-operator is however not such a good idea. Here is why.

## The problem

If you run this little program you will see that the result is not always what you expect.

```vbnet
Option Strict Off
Option Infer On

Module Module1

	Sub Main()
		Dim _I = "1" + 1
		Console.WriteLine(_I)
		Console.WriteLine("1" + 1)
		Console.WriteLine("1" + "1")
		Console.WriteLine("1" & 1)
		Console.WriteLine("1" & "1")
		Console.ReadLine()
	End Sub

End Module
```
The result being.

> 2
  
> 2
  
> 11
  
> 11
  
> 11 

Why the +-operator infers a double here instead of a string is less important. Knowing that it does so is more important, you can [read more about it on MSDN.][2]

Where you will also see this little quote.

> However, to eliminate ambiguity, you should use the & operator instead. If the components of an expression created with the + operator are numeric, the arithmetic result is assigned. If the components are exclusively strings, the strings are concatenated.

So I would recommend not using the + sign to concatenate strings.

## The solution

The solution is called [Resharper][3]. Resharper and the custom patterns (this could have been a book title) to be exact. And this is the custom pattern you can use.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/resharper/image1.png?mtime=1312198816"><img alt="" src="/wp-content/uploads/users/chrissie1/resharper/image1.png?mtime=1312198816" width="800" height="600" /></a>
</div>

As you can see I have also done a replace already. So next time you use a plus you will see this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/resharper/image2.png?mtime=1312198826"><img alt="" src="/wp-content/uploads/users/chrissie1/resharper/image2.png?mtime=1312198826" width="960" height="111" /></a>
</div>

Alas the replace does not work well in Resharper 6.0. But I have filed a bug and I hope this will be fixed soon.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/resharper/image3.png?mtime=1312198835"><img alt="" src="/wp-content/uploads/users/chrissie1/resharper/image3.png?mtime=1312198835" width="1494" height="80" /></a>
</div>

And here you have the pattern you can import into your patterns. Just save it in an xml-file and import.

```xml
&lt;CustomPatterns&gt;
  Pattern Severity="WARNING" FormatAfterReplace="True" ShortenReferences="True" Language="VBASIC"&gt;
    &lt;Comment&gt;String concatenation with a plus is highly discouraged try to use &.&lt;/Comment&gt;
    &lt;ReplacePattern&gt;&lt;![CDATA[$str1$ & $str2$]]&gt;&lt;/ReplacePattern&gt;
    &lt;SearchPattern&gt;$str1$ + $str2$&lt;/SearchPattern&gt;
    &lt;Params /&gt;
    &lt;Placeholders&gt;
      &lt;ExpressionPlaceholder Name="str1" ExpressionType="System.String" ExactType="False" /&gt;
      &lt;ExpressionPlaceholder Name="str2" ExpressionType="System.String" ExactType="False" /&gt;
    &lt;/Placeholders&gt;
  &lt;/Pattern&gt;
&lt;/CustomPatterns&gt;```
## Conclusion

String concatenation with the +-operator is to be avoided in VB.Net so use the &-operator instead. And you can use Resharper to avoid from making this mistake.

 [1]: /index.php/All/?p=1292
 [2]: http://msdn.microsoft.com/en-us/library/9c5t70w2%28v=VS.71%29.aspx
 [3]: http://www.jetbrains.com/resharper/