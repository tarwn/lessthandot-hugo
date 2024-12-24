---
title: SQLCop is now available via chocolatey
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-16T08:35:00+00:00
ID: 1384
excerpt: I added an sqlcop package to the chocolatey repository.
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sqlcop-is-now-available-via/
views:
  - 1472
categories:
  - Microsoft SQL Server Admin
tags:
  - chocolatey
  - sqlcop

---
Here is the definition of [chocolatey][1] as stated on the [chocolatey.org][2] website.

> Chocolatey NuGet is a Machine Package Manager, somewhat like apt-get, but built with windows in mind.

Chocolatey can be easily installed.

> To install chocolatey now, open a powershell prompt (execution policy needs to be unrestricted &#8211; Set-ExecutionPolicy Unrestricted), paste the following and type Enter:
> 
> PS:> iex ((new-object net.webclient).DownloadString(&#8220;http://bit.ly/psChocInstall&#8221;)) 

And Chocolatey also has [a GUI][3] if you like those kinds of things. Yeah I wrote that GUI, sorry.

And now I added [sqlcop to chocolatey][4] which makes it dead easy to install.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/sqlcopchocolatey1.png?mtime=1321439578"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/sqlcopchocolatey1.png?mtime=1321439578" width="1039" height="629" /></a>
</div>

Just type <code class="codespan">cinst sqlcop</code> in your favorite command prompt or use the GUI and sqlcop will be there for you. It will also place a shortcut on your desktop for you to have easy access to sqlcop.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/sqlcopchocolatey2.png?mtime=1321439592"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/sqlcopchocolatey2.png?mtime=1321439592" width="430" height="374" /></a>
</div>

So now you have no more excuse not to install chocolatey and sqlcop.

[Making a chocolatey package][5] is easy BTW.

 [1]: /index.php/DesktopDev/MSTech/chocolatey-apt-get-for-windows?highlight=chocolatey&sentence=
 [2]: http://chocolatey.org
 [3]: /index.php/DesktopDev/MSTech/chocolatey-gui?highlight=chocolatey&sentence=
 [4]: http://chocolatey.org/packages/sqlcop
 [5]: /index.php/DesktopDev/MSTech/making-a-chocolatey-package?highlight=chocolatey&sentence=