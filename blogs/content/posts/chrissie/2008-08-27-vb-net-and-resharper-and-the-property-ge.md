---
title: VB.Net and Resharper and the Property generation thing
author: Christiaan Baes (chrissie1)
type: post
date: 2008-08-27T05:15:39+00:00
ID: 115
url: /index.php/desktopdev/mstech/vb-net-and-resharper-and-the-property-ge/
views:
  - 6592
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - generate
  - property
  - reshaper
  - vb.net

---
In my [last blogpost][1] I talked about property generation and how the naming for the generated property seemed a bit strange. Luckily the guys from [JetBrains][2] ([Ilya Ryzhenkov][3]) read our blog 😉 and gave a [comment][4].

So I acted on it and found what he was talking about. I always use the prefix with underscore and have camelcasing because I have NHibernate setup like that too. And it becomes a habit. So I just had to add the underscore to the namingstyle, like shown in the picture.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/ResharperNamingStyle.jpg" alt="" title="" width="702" height="428" />
</div>

And then this

```vbnet
Private _color As String```
Quickly turns into this.

```vbnet
Public Property Color() As String
  Get
    Return _color
  End Get
  Set (ByVal value As String)
    _color = value
  End Set
End Property```
Isn&#8217;t that cool?

 [1]: /index.php/DesktopDev/MSTech/let-resharper-do-the-heavy-lifting-for-y
 [2]: http://www.jetbrains.com
 [3]: http://resharper.blogspot.com/
 [4]: /index.php/DesktopDev/MSTech/let-resharper-do-the-heavy-lifting-for-y#c155