---
title: .Net and a problem with unicode and ToLower and ToUpper
author: Christiaan Baes (chrissie1)
type: post
date: 2008-10-28T07:19:22+00:00
ID: 185
url: /index.php/architect/introductionarchitecturedesign/net-and-a-problem-with-unicode-and-tolow/
views:
  - 4171
categories:
  - Introduction to Architecture and Design

---
I am watching another [dnrTV][1] webcast, this time featuring [Kathleen Dollard][2]. The webcast is about the [difference between VB.Net and C# for the .Net 3.5 framework][3].

An interesting point came up where Kathleen says that casting to lower with unicode can be a security risk, more so then casting to upper.

She was talking about an article in Visual Studio magazine by Bill McCarthy about this subject but I can&#8217;t find it.

Anyway, it is always better to use string.EqualsIgnoreCase(&#8220;test&#8221;) than it is to do string.ToLower.Equals(&#8220;test&#8221;). I&#8217;ll just take her word for it ;-).

EDIT: Apparently Remou has beter Google skills than me.

And here is his explanation from [that page][4].

> **It depends a lot on the locale. For example, in some locales é will uppercase to E, yet E will lowercase to e. If you compare the lowercase of E to é, it won&#8217;t be a match. Uppercase, on the other hand, will generally match. The use of ToUpper can be handy in circumstances like this one:</p> 
> 
> Select Case myString.ToUpper
	  
> Case &#8220;ABC&#8221;
	  
> Case &#8220;DEF&#8221;, &#8220;FGH&#8221;
	  
> Case &#8220;IJK&#8221;
  
> &#8216; &#8230;
> 
> You can also specify the culture to use:
> 
> mystring.ToUpper
  
> (Globalization.CultureInfo.InvariantCulture)
> 
> For the most part, using ToUpper won&#8217;t cause any problems, but the ToUpper call does cause a new string to be created, so it can get expensive if you use it repeatedly. With some locales, and with special characters such as the German letter ß, ToUpper won&#8217;t give the same results as the Compare or Equals methods. That&#8217;s why I said in the article to consider using String.Compare instead. Consider how things change if we write the ToUpper code sample using String.Compare or String.Equals:
> 
> If String.Equals(myString, &#8220;ABC&#8221;,
  
> StringComparison.OrdinalIgnoreCase) Then
  
> ElseIf String.Equals(myString, &#8220;DEF&#8221;,
           
> StringComparison.OrdinalIgnoreCase) _
	  
> OrElse String.Equals(myString, &#8220;FGH&#8221;,
  
> StringComparison.OrdinalIgnoreCase) _
      
> Then
  
> ElseIf String.Equals(myString, &#8220;IJK&#8221;,
     
> StringComparison.OrdinalIgnoreCase) Then
  
> &#8216; &#8230;
  
> End If
> 
> I think I&#8217;d much rather suffer a little performance hit for the sake of readability.</strong> </blockquote>

 [1]: http://www.dnrTV.com
 [2]: http://msmvps.com/blogs/kathleen/default.aspx
 [3]: http://perseus.franklins.net/dnrtvplayer/player.aspx?ShowNum=0097
 [4]: http://visualstudiomagazine.com/columns/article.aspx?editorialsid=2334