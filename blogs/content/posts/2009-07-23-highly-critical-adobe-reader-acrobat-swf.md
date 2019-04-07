---
title: Highly Critical Adobe Reader/Acrobat SWF Content Arbitrary Code Execution Vulnerability
author: SQLDenis
type: post
date: 2009-07-23T15:09:33+00:00
ID: 521
url: /index.php/itprofessionals/ethicsit/highly-critical-adobe-reader-acrobat-swf/
views:
  - 4587
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
tags:
  - adobe
  - security

---
Another critical Adobe Reader Vulnerability.

A vulnerability has been reported in Adobe Reader and Acrobat, which can be exploited by malicious people to compromise a user&#8217;s system.

The vulnerability is caused due to an error in authplay.dll when processing SWF content and can be exploited to execute arbitrary code.

From Adobe&#8217;s [security advisory][1] 

> A critical vulnerability exists in the current versions of Flash Player (v9.0.159.0 and v10.0.22.87) for Windows, Macintosh and Linux operating systems, and the authplay.dll component that ships with Adobe Reader and Acrobat v9.x for Windows, Macintosh and UNIX operating systems. This vulnerability (CVE-2009-1862) could cause a crash and potentially allow an attacker to take control of the affected system. There are reports that this vulnerability is being actively exploited in the wild via limited, targeted attacks against Adobe Reader v9 on Windows.
> 
> Deleting, renaming, or removing access to the authplay.dll file that ships with Adobe Reader and Acrobat v9.x mitigates the threat for those products, but users will experience a non-exploitable crash or error message when opening a PDF that contains SWF content. Depending on the product, the authplay.dll that ships with Adobe Reader and Acrobat 9.x for Windows is typically located at C:Program FilesAdobeReader 9.0Readerauthplay.dll or C:Program FilesAdobeAcrobat 9.0]Acrobatauthplay.dll. Windows Vista users should consider enabling UAC (User Access Control) to mitigate the impact of a potential exploit. Flash Player users should exercise caution in browsing untrusted websites. Adobe is in contact with Antivirus and Security vendors regarding the issue and recommend users keep their anti-virus definitions up to date.

Fix will be available by July 31st, learn more here: http://www.adobe.com/support/security/advisories/apsa09-03.html

Exploit code is already available on milw0rm: http://milw0rm.com/exploits/9233 **I would not recommend trying that out!!**

 [1]: http://www.adobe.com/support/security/advisories/apsa09-03.html