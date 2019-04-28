---
title: Winforms ReportViewer not working in VS2010
author: Christiaan Baes (chrissie1)
type: post
date: 2010-05-03T05:37:31+00:00
ID: 776
url: /index.php/desktopdev/mstech/winforms-reportviewer-not-working-in-vs2/
views:
  - 8235
categories:
  - Microsoft Technologies
tags:
  - rdlc
  - reportviewer
  - vb.net

---
I upgraded to VS2010 a few weeks ago and everything went pretty OK. Until today. Today I wanted to add another report, which was no big deal. Making the report is the same as in VS2008, no improvements there. And then I tried to run the report. And all was not so well. I got this.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Reportviewer/Reportviewer2008.png" alt="" title="" width="467" height="475" />
</div>

With the error message being.

> An error occurred during local report processing. The definition of the report &#8216;Main Report&#8217; is invalid. The report definition is not valid. Details: The report definition has an invalid target namespace &#8216;http://schemas.microsoft.com/sqlserver/reporting/2008/01/reportdefinition&#8217; which cannot be upgraded.

Apparently VS2010 makes its reports in the SSRS 2008 format which the VS2008 reportviewer can&#8217;t read. So the problem is that I still had v9.0.0.0 of the reportviewer referenced and it needed a reference to the 10.0.0.0. No problem since version 10.0.0.0 was installed when I installed VS2010. But if you so like it is also available as a [separate download][1].

As you can see I have both versions on my system. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Reportviewer/Reportviewer20082.png" alt="" title="" width="561" height="446" />
</div>

I deleted the old one and added the new reference to my project.

And now the reportviewer just works.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Reportviewer/Reportviewer20083.png" alt="" title="" width="392" height="475" />
</div>

I guess you&#8217;ll have to trust me on this ;-).

> <span class="MT_red">I also noticed that the VS2010 update wizard does not update rdlc reports to the VS2010 format. But when you open an rdlc it will ask you to upgrade to the new format. If you decide to upgrade the report you will also need to upgrade the reportviewer which is not something it tells you at the time.</span>

 [1]: http://www.microsoft.com/downloads/details.aspx?FamilyID=a941c6b2-64dd-4d03-9ca7-4017a0d164fd&displaylang=en