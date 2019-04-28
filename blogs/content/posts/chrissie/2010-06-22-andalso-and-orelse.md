---
title: AndAlso and OrElse
author: Christiaan Baes (chrissie1)
type: post
date: 2010-06-22T18:31:50+00:00
ID: 826
url: /index.php/desktopdev/mstech/andalso-and-orelse/
views:
  - 3764
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - andalso
  - vb.net

---
I still see too many people use <span class="MT_blue">And</span> and <span class="MT_blue">Or</span> when beginning with VB.Net.

Since the introduction of VB.Net, way back in 2002, there have been <span class="MT_blue">AndAlso</span> and <span class="MT_blue">OrElse</span>. In most cases you should prefer them over <span class="MT_blue">And</span> and <span class="MT_blue">Or</span>. 

Now why would they have picked a less logical name for something you should actually use more? As always it is simple, backward comparability. And it goes back to the beginning of the B in VB. The keywords and their behavior has been in there since pretty much the beginning. So why change it? It would seem logical now to change it, but it wasn&#8217;t way back in 2002. 

You can read the exact reasoning from [Paul Vick himself][1].

When working with reference types you should probably always use <span class="MT_blue">AndAlso</span>. Because it is more of a no-brainer than <span class="MT_blue">And</span>. Just look at this:

```vbnet
If person IsNot Nothing And person.Name.Equals("Baes") Then
...```
The above code will throw an exception when person is Nothing. Because both sides of the <span class="MT_blue">And</span> will be evaluated no matter what. To avoid this you could write:

```vbnet
If person IsNot Nothing Then
If person.Name.Equals("Baes") Then
...```
But that seems wrong.

Or you could write.

```vbnet
If person IsNot Nothing AndAlso person.Name.Equals("Baes") Then
...```
And not get an exception because <span class="MT_blue">AndAlso</span> will only execute the first part, see that it doesn&#8217;t comply with the rules and will no longer evaluate the second part.

It all seems simple enough and it is simple enough. I just see that people keep forgetting it.

BTW: Paul Vick is now on the T-SQL team, and we know there is lots to improve there ;-).

 [1]: http://www.panopticoncentral.net/articles/919.aspx