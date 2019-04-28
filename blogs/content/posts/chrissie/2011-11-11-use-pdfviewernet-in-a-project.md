---
title: use pdfviewernet in a project for .net framework 4.0
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-11T07:02:00+00:00
ID: 1381
excerpt: How to run pdfviewernet from a .net 4.0 project.
url: /index.php/desktopdev/mstech/use-pdfviewernet-in-a-project/
views:
  - 14189
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - .net 4.0
  - pdfviewernet
  - vb.net

---
So I downloaded the source for [pdfviewernet][1] and just [ran it out of the box][2]. 

And it worked.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet2.png?mtime=1320913623"><img alt="" src="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet2.png?mtime=1320913623" width="790" height="640" /></a>
</div>

If you go look in the properties of the Samplepdfviewer you will notice that it is set to run for a .net 2.0 framework.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet4.png?mtime=1321000462"><img alt="" src="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet4.png?mtime=1321000462" width="680" height="522" /></a>
</div>

You might want to change that to .net 4.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet5.png?mtime=1321000725"><img alt="" src="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet5.png?mtime=1321000725" width="680" height="522" /></a>
</div>

If you try to run this now you will get this error.

> An error occurred creating the form. See Exception.InnerException for details. The error is: Mixed mode assembly is built against version &#8216;v2.0.50727&#8217; of the runtime and cannot be loaded in the 4.0 runtime without additional configuration information.

But that is easily fixed by adding the following line to your app.config.

```xml
&lt;?xml version="1.0"?&gt;
&lt;configuration&gt;
  &lt;startup useLegacyV2RuntimeActivationPolicy="true"&gt;
    &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/&gt;
  &lt;/startup&gt;
&lt;/configuration&gt;
```
The part with <code class="codespan">useLegacyV2RuntimeActivationPolicy="true"</code> is important since you might already have the other bits.

If you run it now it will just work. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet6.png?mtime=1321001034"><img alt="" src="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet6.png?mtime=1321001034" width="727" height="533" /></a>
</div>

Until you click on the browse button and select a file and click Ok. You will then get the following error.

> Declaration referenced in a method implementation cannot be a final method. Type: &#8216;PDFLibNet.xPDFBinaryReader&#8217;. Assembly: &#8216;PDFLibNet, Version=1.0.6.8, Culture=neutral, PublicKeyToken=26d87f7d66fb2aee&#8217;.

Oh joy. This is also &#8220;easily&#8221; fixed. Just replace the PDFLibnet.dll in <span class="MT_red">your output folder</span> with the one in the PDFView/lib/net4.0/ and then pick the x86 or x64 depending on your system. You can <span class="MT_red">not</span> change the reference in you PDFView project since that expects a .Net 2.0 library. You could change the PDFView to .net 4.0 too and then change the reference but I didn&#8217;t not find that to be necessary.

And if you run it now and open your pdf you will see this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet2.png?mtime=1320913623"><img alt="" src="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet2.png?mtime=1320913623" width="790" height="640" /></a>
</div>

And on your development machine there is a 99% chance that this will work. This will work because like 99% of developers you installed Visual studio with the default parameters. And this means that the VC++ libraries are also installed. You might want to note that your clients will probably not have these installed and it will therefor crash on you when you run it there. It will say that PDFLibNet.dll can not be loaded because it is missing or one of it&#8217;s dependencies is missing. And we all know PDFLibnet is there so it must be the dependencies. 

Those dependencies are the VC++ 2010 libraries. You can easily [download][3] and install them.

And now your clients will have a working pdfviewer and you will have .net 4.0 code to play with, so everyone is happy.

 [1]: http://code.google.com/p/pdfviewernet/
 [2]: /index.php/DesktopDev/MSTech/VBNET/pdfviewernet-where-have-you-been
 [3]: http://www.microsoft.com/download/en/details.aspx?id=5555