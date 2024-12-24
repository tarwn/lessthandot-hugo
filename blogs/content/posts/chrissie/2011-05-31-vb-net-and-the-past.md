---
title: 'VB.Net and the past: The return thing and C#'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-31T10:24:00+00:00
ID: 1195
excerpt: |
  Introduction
  
  VB.Net is not such an old language, let's say it saw the light in 2002, that makes it 9 years old. But it has some compatibility with VB code which makes it 20 years old. So the tech leads of the language don't only add new things they a&hellip;
url: /index.php/desktopdev/mstech/vb-net-and-the-past/
views:
  - 1868
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net

---
## Introduction

VB.Net is not such an old language, let&#8217;s say it saw the light in 2002, that makes it 9 years old. But it has some compatibility with VB code which makes it 20 years old. So the tech leads of the language don&#8217;t only add new things they also have to make sure the old things work and that it is compatible with C#. This sometimes has some weird consequences. 

## The VB code

Just look at these three [functions][1].

```vbnet
Public Function TestReturn() As String
  Return "test"
End Function

Public Function TestNoReturn() As String
  TestNoReturn = "test"
End Function

Public Function TestNoReturnFancy() As String
  TestNoReturnFancy = "test"
  Mid(TestNoReturnFancy, (InStr(TestNoReturn, "te")), 2) = "pe"
End Function```
Those three are all valid code. 

They will return this.

> test
  
> test
  
> pest 

That last function is kind of weird and not recommend. The latest version of Resharper 6 will even tell you it does not understand that and give an error.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_4.png?mtime=1306844014"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_4.png?mtime=1306844014" width="600" height="91" /></a>
</div>

With this errormessage.

> Only variable or property can be the target of an assignment.

I don&#8217;t know if I should file that as a bug to the Resharper team since it is mighty ugly code and not desired.

## The C# code

Luckily you can not do the above in C#. You can just do normal things.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_5.png?mtime=1306844236"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_5.png?mtime=1306844236" width="403" height="200" /></a>
</div>

## Conclusion

Some things should just be deprecated and the assign to functionname is one of them.

 [1]: http://msdn.microsoft.com/en-us/library/sect4ck6%28VS.80%29.aspx