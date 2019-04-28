---
title: InternalsVisibleTo, VB.Net, unittesting and windows forms components
author: Christiaan Baes (chrissie1)
type: post
date: 2010-02-15T09:16:14+00:00
ID: 703
url: /index.php/desktopdev/mstech/vbnet/internalsvisibleto-vb-net-unittesting-an/
views:
  - 4082
categories:
  - VB.NET
tags:
  - internalsvisibleto

---
I am not in the habbit of testing internal/friend methods, properties or anything else. Because I think testing public members should cover those. But sometimes you don&#8217;t really have a choice. In this case it&#8217;s the form designer that is the cause of my problems (not the first time) It by default creates controls and components as Friend which is fine by me since controls can be found via the ControlsCollection. But the components like timer, notifyicon and such are not in that controlscollection and thus can only be found by either resetting their accesslevel or by reflection.

If you want to access them via reflection you have to make sure that the Assembly has an InternalsVisibleTo attribute somewhere. And of course you then go to Google to find out how to do it. And stumble upon [this little MSDN article][1]. The article has a VB.Net section which says that you have to declare it this way.

```vbnet
&lt;Assembly: InternalsVisibleTo("AssemblyName")&gt; 
```
You can do this just about anywhere but I would recommend doing it in the AssemblyInfo.vb file.

You can now access all the internal/Friend fields directly even the controls.

I would still be carefull with this since it defeats the purpose of setting something to Friend/internal.

 [1]: http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.internalsvisibletoattribute.aspx