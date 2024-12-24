---
title: Some bugs just never go away
author: Christiaan Baes (chrissie1)
type: post
date: 2008-11-27T06:23:10+00:00
ID: 222
url: /index.php/desktopdev/mstech/some-bugs-just-never-go-away/
views:
  - 4300
categories:
  - Microsoft Technologies
tags:
  - bug
  - enablevisualstyles
  - treeview

---
Some bugs just don&#8217;t seem to be the trouble to fix.

I had this unusual problem with a treeview (the one that can be found at System.Windows.Forms.TreeView) and disappearing icons. 

Just look at this.

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/treeviewicons1.jpg" alt="" title="" width="112" height="168" />

You can see that there is nothing wrong with the icons or the imagelist because the button just above the treeview uses an icon from the same imagelist as the treeview.

Now this seems to be a known bug. Just look at this post http://forums.microsoft.com/MSDN/ShowPost.aspx?siteid=1&PostID=965968, which is kinda funny because the OP posted on 28 nov 2006. Just 1 day shy of 2 years ago. Which makes it VS 2005. And for Microsoft it&#8217;s a known bug but fixing it is not an issue.

For those who want to fix it. Just remove this line Application.EnableVisualStyles() and then you get the following:

 <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/treeviewicons2.jpg" alt="" title="" width="102" height="167" />

If you really don&#8217;t want to remove that line, then you will have to use another TreeView and ListView because both suffer from the same disease.