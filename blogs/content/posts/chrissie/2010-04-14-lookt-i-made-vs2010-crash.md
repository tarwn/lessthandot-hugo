---
title: Look, I made VS2010 crash.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-14T09:21:58+00:00
ID: 759
url: /index.php/desktopdev/mstech/lookt-i-made-vs2010-crash/
views:
  - 4576
categories:
  - Microsoft Technologies
tags:
  - vb.net
  - vs2010

---
Today I opened up my solution in VS2010 and did the upgrade, which according to the upgrade wizard was a minor thing.

All projects are still compiled against the 3.5 framework. So there should not be any difference.

And Still&#8230; it crashed.

Well it wasn&#8217;t really me, but Something in the resx from a third party. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/VS2010/VS2010error.png" alt="" title="" width="376" height="180" />
</div>

And the error is very descriptive too.

> Error 2 The type initializer for &#8216;Atalasoft.Imaging.AtalaImage&#8217; threw an exception. Line 133, position 5. E:Visual studio projectsTDB2009ProjectsTDB2009.ViewKitManagementViewDialogsfrmScanReciepts.resx 133 5 TDB2009.View

I guess I just have to delete the offending code then.

```xml
&lt;assembly alias="Atalasoft.dotImage" name="Atalasoft.dotImage, Version=3.0.2312.18597, Culture=neutral, PublicKeyToken=2b02b46f7326f73b" /&gt;
  &lt;data name="lstThumbs.ErrorImage" type="Atalasoft.Imaging.AtalaImage, Atalasoft.dotImage" mimetype="application/x-microsoft.net.object.bytearray.base64"&gt;
    &lt;value&gt;
        iVBORw0KGgoAAAANSUhEUgAAAGQAAABkCAIAAAD/gAIDAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAAB3RJ
        TUUH1wkMBic34twwJgAAAaFJREFUeJzt1ltOwzAUAFEXdTvsfyHeEB+RzMV2nTuEkFSa89WG+MGQWDxq
        rUU5H1dv4J18x/r86cikB4e3GX4xz/GlF57xy91eybvtx9cQeK5/vD3Vtdb2oYRHPX7dfQq6Uesr7eu2
        dLeB7s7p0nHyV2MXs433lC7W9I72uc0S19g+Z0otRsUo4+7jkN07o27D+XWnuy3Xnln5w/hvN5ZZd3rP
        zmt4nuTTcdW60z+PB/yOGHR+ZpXXT347Lxf3jFONo+KVcea1/J2Zsesr8Xd83O1/GSq+VmfPdtmZdUTm
        DThjtrd/sv6TBzxgLMBYgLEAYwHGAowFGAswFmAswFiAsQBjAcYCjAUYCzAWYCzAWICxAGMBxgKMBRgL
        MBZgLMBYgLEAYwHGAowFGAswFmAswFiAsQBjAcYCjAUYCzAWYCzAWICxAGMBxgKMBRgLMBZgLMBYgLEA
        YwHGAowFGAswFmAswFiAsQBjAcYCjAUYCzAWYCzAWICxAGMBxgKMBRgLMBZgLMBYgLEAYwHGAowFGAsw
        FmAswFiAsQBjAcYCvgC3UMHiCSbL7wAAAABJRU5ErkJggg==
&lt;/value&gt;
  &lt;/data&gt;```
After deleting that code the build just worked. Still find it a bit strange.