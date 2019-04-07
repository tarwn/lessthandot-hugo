---
title: Microsoft bans memcpy() to minimize buffer overflows
author: SQLDenis
type: post
date: 2009-05-18T00:15:02+00:00
ID: 434
excerpt: 'Microsoft announced that they have added memcpy() to their list of banned functions. The function memcpy() has been responsible for a bunch of security problems in Microsoft and third party products. The reason for this is that memcpy() is not safe and&hellip;'
url: /index.php/enterprisedev/appserver/microsoft-bans-memcpy-to-minimize-buffer/
views:
  - 24160
rp4wp_auto_linked:
  - 1
categories:
  - Application Server
tags:
  - buffer overflow
  - security

---
Microsoft announced that they have added **memcpy()** to their list of banned functions. The function memcpy() has been responsible for a bunch of security problems in Microsoft and third party products. The reason for this is that memcpy() is not safe and can cause a buffer overflow. So what is a buffer overflow anyway? Here is what wikipedia has on [buffer overflow][1]

> In computer security and programming, a buffer overflow, or buffer overrun, is an anomaly where a process stores data in a buffer outside the memory the programmer set aside for it. The extra data overwrites adjacent memory, which may contain other data, including program variables and program flow control data. This may result in erratic program behavior, including memory access errors, incorrect results, program termination (a crash), or a breach of system security.
> 
> Buffer overflows can be triggered by inputs that are designed to execute code, or alter the way the program operates. They are thus the basis of many software vulnerabilities and can be maliciously exploited. Bounds checking can prevent buffer overflows.
> 
> Programming languages commonly associated with buffer overflows include C and C++, which provide no built-in protection against accessing or overwriting data in any part of memory and do not automatically check that data written to an array (the built-in buffer type) is within the boundaries of that array.

Here is a video of a UltraVNC v1.01 client buffer overflow by using Metasploit.
  


You can also check [milw0rm][2] where they list security vulnerabilities exploits, count the buffer overflow vulnerabilities.

Here is a nice one: [AOL IWinAmpActiveX Class (AmpX.dll 2.4.0.6) ConvertFile() remote overflow exploit (IE6/IE7)][3]

So now that memcpy() is banned what should you use? You should use memcpy\_s(). The function memcpy\_s() takes one extra parameter: this extra parameter is the size of the destination buffer.

You can read more about this here: http://blogs.msdn.com/sdl/archive/2009/05/14/please-join-me-in-welcoming-memcpy-to-the-sdl-rogues-gallery.aspx

 [1]: http://en.wikipedia.org/wiki/Buffer_overflow
 [2]: http://milw0rm.com/
 [3]: http://milw0rm.com/exploits/8733