---
title: How to export an outlook calendar to word
author: Christiaan Baes (chrissie1)
type: post
date: 2008-11-24T08:23:28+00:00
ID: 216
url: /index.php/desktopdev/mstech/vbaformsoffice/how-to-export-an-outlook-calendar-to-wor/
views:
  - 6064
categories:
  - VBA for Microsoft Office Products
tags:
  - 2003
  - office
  - outlook
  - template
  - word

---
Someone today asked me how to copy paste the calendar in outlook to a word document. I couldn&#8217;t find the answer to that question but I found a suitable alternative instead. 

The alternative is to use [this dot (template)][1] file instead. I found it on [this Microsoft site.][2]. 

This isn&#8217;t made for us europeans, as the Note says.

> NOTE: This template solution is designed to work only with U.S. versions of Microsoft Windows and Microsoft Office. It may not function correctly with non-U.S. versions of these products. 

But that is easily fixed.

I changed this

```vbnet
argdate = Trim(cmonth) & "/" & "1" & "/" & Trim(styear)```
into this

```vbnet
argdate = "1" & "/" & Trim(cmonth) & "/" & Trim(styear)```
The code is horrible BTW. But it works. And you have to turn on macros for this to work. 

That site says it only works with 2002 but I tried it on 2003 and it works just as good.

 [1]: http://download.microsoft.com/download/outlook2000/utility/1.0/win98me/en-us/olcalndr.exe
 [2]: http://support.microsoft.com/kb/290614/en-us