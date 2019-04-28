---
title: 'VB.Net: Resharper SurroundTemplate for Try Catch'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-03T09:55:31+00:00
ID: 125
url: /index.php/desktopdev/mstech/vb-net-resharper-surroundtemplate-for-tr/
views:
  - 3261
categories:
  - Microsoft Technologies
tags:
  - resharper
  - surround
  - template
  - vb.net

---
Apparently someone at [JetBrains][1] forgot to add a surround template for Try catch in VB.Net. It is there for C#. So I created it for myself.

This is the xml. Just save it in a file and import it in resharper.

```xml
&lt;TemplatesExport family="Surround Templates"&gt;
  &lt;Template uid="febffd83-d964-4b7d-96fe-abd071be9c50" text="Try&#xD;&#xA;	$SELECTION$&#xD;&#xA;Catch ex As Exception&#xD;&#xA;&#xD;&#xA;End Try" shortcut="" description="Try...Catch" reformat="true" shortenQualifiedReferences="true"&gt;
    &lt;Context&gt;
      &lt;VBContext context="Expression" /&gt;
    &lt;/Context&gt;
    &lt;Categories /&gt;
    &lt;Variables /&gt;
    &lt;CustomProperties /&gt;
  &lt;/Template&gt;
&lt;/TemplatesExport&gt;```

 [1]: http://www.jetbrains.com/