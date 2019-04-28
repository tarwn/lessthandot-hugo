---
title: An Aha moment and NUnit categories
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-09T06:40:36+00:00
ID: 417
excerpt: |
  Life can be so simple sometimes but you just have to think about it.
  
  I found it very cool when Sean Feldman posted this CategoryAttribute. Yeah I know the title doesn't say everything and the post iskinda short but very sweet to me. More DRYness to p&hellip;
url: /index.php/enterprisedev/unittest/an-aha-moment-and-nunit-categories/
views:
  - 9483
categories:
  - Unit Testing
tags:
  - categories
  - nunit
  - vb.net

---
Life can be so simple sometimes but you just have to think about it.

I found it very cool when [Sean Feldman][1] posted this [CategoryAttribute][2]. Yeah I know the title doesn&#8217;t say everything and the post iskinda short but very sweet to me. More DRYness to play with.

Now let me translate his post into VB.Net.

How not to do it.

```vbnet
&lt;Category("Integration")&gt; _
```
How to do it.

```vbnet
&lt;Category(Categories.Integration)&gt; _```
where Categories is a sealed class with constants

```vbnet
Namespace NUnitCategories
    Public NotInheritable Class Categories
        Public Const Integration As String = "Integration"
    End Class
End Namespace```

 [1]: http://weblogs.asp.net/sfeldman/default.aspx
 [2]: http://weblogs.asp.net/sfeldman/archive/2009/05/07/categoryattribute.aspx