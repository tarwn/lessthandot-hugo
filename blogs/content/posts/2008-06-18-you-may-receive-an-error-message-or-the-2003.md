---
title: You may receive an error message, or the computer may stop responding, when you host Web applications that use ASP.NET on a computer that is running Windows Server 2003
author: SQLDenis
type: post
date: 2008-06-18T12:11:05+00:00
ID: 65
url: /index.php/webdev/webdesigngraphicsstyling/you-may-receive-an-error-message-or-the-2003/
views:
  - 3547
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - asp.net
  - bug
  - windows server 2003

---
Got an email from a friend who is suffering from this problem

**Here are the symptoms**
  
When you host Web applications that use Microsoft ASP.NET on a computer that is running Microsoft Windows Server 2003, you may experience decreased performance. This issue may occur when you host the Web applications in multiple application pools on a multiprocessor computer. Additionally, you may experience one or more of the following issues when available memory is low:
  
• You may receive exceptions of type System.OutOfMemoryException.
  
• You may receive the following error message when you try to open an ASP.NET Web page:
  
Server Application Unavailable
  
• The computer may stop responding. 

**And here is why**
  
These issues occur because the Microsoft .NET Framework common language runtime (CLR) uses the Server garbage collector (GC) on multiprocessor computers. This is the default behavior. The Server garbage collector is optimized for scalable throughput on multiprocessor computers. To reduce contention and to improve garbage collector performance on multiprocessor computers, the Server garbage collector creates one heap per processor for parallel collections. Therefore, the Server garbage collector consumes lots of memory when you host multiple ASP.NET worker processes. This behavior may cause the issues that are described in the “Symptoms&#8221; section.

Microsoft has published a knowledge base article, you can read the article here: http://support.microsoft.com/kb/911716

The article has the workaround for this problem