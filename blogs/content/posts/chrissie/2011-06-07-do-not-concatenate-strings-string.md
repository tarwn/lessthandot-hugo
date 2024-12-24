---
title: Do not concatenate strings String.Format instead and a bit of resharper magic to help us.
author: Christiaan Baes (chrissie1)
type: post
date: 2011-06-07T09:15:00+00:00
ID: 1208
excerpt: |
  Introduction
  
  We all know string concatenation is bad when working with SQL-statements because of the sql-injection that can occur. So we avoid that by using parameters. But string concatenation is bad in all your code because of the immutability of s&hellip;
url: /index.php/desktopdev/mstech/do-not-concatenate-strings-string/
views:
  - 30124
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - string.format
  - vb.net

---
## Introduction

We all know string concatenation is bad when working with SQL-statements because of the sql-injection that can occur. So we avoid that by using parameters. But string concatenation is bad in all your code because of the [immutability of strings][1]. You can avoid those bad things by using String.Format or StringBuilder. 

But it ain&#8217;t all that simple why and when you want and need to use either of the two. After all readibility and maintainability are rarely a consideration for the two.

Here are some things to read.

  * [What&#8217;s the best string concatenation method using C#?][2] on [Stackoverflow][3]
  * [More StringBuilder advice][4] by [Rico Mariani][5]
  * [String Formatting in C#][6] by [SteveX][7]
  * [String Formatting FAQ][8] by [SteveX][7]

If you read the above blogs the need for Stringbuilder is mainly a speed thing. But it only helps much when you have enough concatenations and then some. It is still good practice to use the stringbuilder if you need to concatenate several strings together. And in the programming world 99% of string need concatenating.

## String.Format

Now why would I prefer for you to use String.Format over a normal concatenation? 

String concatenation

```vbnet
Dim s1 as String = "1"
Dim s2 as String = "1"
Dim s3 as String = "1"
Dim s4 as String = "1"
Dim s5 as String
s5 = s1 & " " & s2 & " " & s3 & " " & s4```
String.Format

```vbnet
Dim s1 as String = "1"
Dim s2 as String = "1"
Dim s3 as String = "1"
Dim s4 as String = "1"
Dim s5 as String
s5 = string.Format("{0} {1} {2} {3}",s1, s2 ,s3 ,s4)
```
The result of the two statements is the same and we don&#8217;t really do premature optimization so we don&#8217;t care which one of the two is faster. But the String.Format one just seems to be better at telling me what it does. So I prefer that one. 

## Resharper

Now that I know I want my code to use either stringbuilder, String.Format or parameters (in the case of sql-statements) I must make sure that my fellow programmers (being me) don&#8217;t mess it up. And that&#8217;s where resharper and the custom patterns come in.

To make a custom pattern you go to the Resharper Options menu.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns1.png?mtime=1307444619"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns1.png?mtime=1307444619" width="700" height="461" /></a>
</div>

And then you need to add a pattern. For which I got [a little help][9].

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns2.png?mtime=1307444722"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns2.png?mtime=1307444722" width="1016" height="600" /></a>
</div>

Like I did the above. You can find more details on how to this at [Hadi&#8217;s place.][10]

So I&#8217;m looking for <code class="codespan">&</code> with a string in front and behind it. str1 and str2 are placeholders of type expression and they should match System.String or derived type of that.

Now when I do a search it will find all the concatenations in my code.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns3.png?mtime=1307445031"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns3.png?mtime=1307445031" width="714" height="571" /></a>
</div>

But that is not all. As you can see above I also set the severity level to warning, so the resharper code inspection thing will now also show a blue squigly line and give me the message I typed in. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns4.png?mtime=1307445123"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns4.png?mtime=1307445123" width="512" height="59" /></a>
</div>

And even better is that you can do Alt+Enter and have it fix it for you by choosing &#8220;Use format string for all arguments&#8221;.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns5.png?mtime=1307449847"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns5.png?mtime=1307449847" width="613" height="141" /></a>
</div>

and it goes from this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns6.png?mtime=1307449977"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns6.png?mtime=1307449977" width="893" height="29" /></a>
</div>

To this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns7.png?mtime=1307449989"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharpercustompatterns7.png?mtime=1307449989" width="999" height="23" /></a>
</div>

In a matter of a second.

## Conclusion

There are so many things we need to know and do but from time to time it is best to have tools to help us do our jobs better and faster. So use those tools. Of course learning those tools takes time and effort.

 [1]: /index.php/Architect/DesigningSoftware/asking-the-right-question?highlight=mutable&sentence=
 [2]: http://stackoverflow.com/questions/21078/whats-the-best-string-concatenation-method-using-c
 [3]: http://stackoverflow.com/
 [4]: http://blogs.msdn.com/b/ricom/archive/2003/12/15/43628.aspx
 [5]: http://blogs.msdn.com/b/ricom/
 [6]: http://blog.stevex.net/string-formatting-in-csharp/
 [7]: http://blog.stevex.net/
 [8]: http://blog.stevex.net/2007/09/string-formatting-faq/
 [9]: http://devnet.jetbrains.net/message/5305670#5305670
 [10]: http://devlicio.us/blogs/hadi_hariri/archive/2010/08/19/highlighting-custom-patterns-with-resharper.aspx