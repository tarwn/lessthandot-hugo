---
title: Studioshell more powershell for Visual studio
author: Christiaan Baes (chrissie1)
type: post
date: 2012-02-10T17:21:00+00:00
ID: 1519
excerpt: "You can never have enough powershell. And it's even cooler if you can have it straight in Visualstudio so that you don't have to leave your most hated/loved IDE. Studioshell is such another addin that makes it possible to use powershell."
url: /index.php/desktopdev/mstech/studioshell-more-powershell-for-visual/
views:
  - 8486
categories:
  - Microsoft Technologies
tags:
  - powershell
  - studioshell
  - visual studio

---
You can never have enough powershell. And it&#8217;s even cooler if you can have it straight in Visualstudio so that you don&#8217;t have to leave your most hated/loved IDE. Studioshell is such another addin that makes it possible to use powershell. 

You can download [studioshell on codeplex][1] (Its the big green button that say download, just in case you were wondering what to click). 

After you install it you will see this In your View menu.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell1.png?mtime=1328899514"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell1.png?mtime=1328899514" width="361" height="117" /></a>
</div>

After you click that menuitem, you will be welcomed by this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell2.png?mtime=1328899527"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell2.png?mtime=1328899527" width="628" height="284" /></a>
</div>

You now have to close Visual studio and restart it and run it as administrator. You can then use studioshell to execute set-executionpolicy &#8216;Unrestricted&#8217; or set-executionpolicy &#8216;RemoteSigned&#8217;

After that you can close Visual studio again and start it normally.

you will then be welcomed by studioshell like this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell3.png?mtime=1328899537"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell3.png?mtime=1328899537" width="540" height="190" /></a>
</div>

And the question on everyones lips&#8230; does this also work with [posh-git][2].

Yes.

But if you get this when you install.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell4.png?mtime=1328900744"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell4.png?mtime=1328900744" width="594" height="485" /></a>
</div>

Then you forgot to unblock your files when you downloaded them, silly you.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell5.png?mtime=1328900754"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell5.png?mtime=1328900754" width="356" height="121" /></a>
</div>

And here is proof that it works.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell6.png?mtime=1328900767"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/studioshell/studioshell6.png?mtime=1328900767" width="606" height="103" /></a>
</div>

So more options is a good thing.

 [1]: http://studioshell.codeplex.com/
 [2]: https://github.com/dahlbyk/posh-git