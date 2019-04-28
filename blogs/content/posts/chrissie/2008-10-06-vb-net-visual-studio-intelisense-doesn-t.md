---
title: 'VB.Net: Visual studio intellisense doesn’t like lambda expressions'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-10-06T11:28:01+00:00
ID: 164
url: /index.php/desktopdev/mstech/vb-net-visual-studio-intelisense-doesn-t/
views:
  - 5489
categories:
  - Microsoft Technologies

---
Well, not in this case anyway.

I have this Rhino mocks method that uses a lambda expression. Like this:

```vb.net
_MspFactory.Stub(Function(e) e.CrudLocus).Return(_CrudILocus)```
And that happens to work and compile. However, intellisense doesn&#8217;t seem to be able to infer the type of e like the compiler seems to be able to do. Just look at picture 1.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/lambdanotinfer1.jpg" alt="" title="" width="434" height="116" />
</div>

But when I specify the type, it is happy to add the intellisense.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/lambdanotinfer2.jpg" alt="" title="" width="747" height="152" />
</div>

Something to work on for version 2010 boys and girls. Let the inferring begin ;D.