---
title: Application.StartupPath and Nunit
author: Christiaan Baes (chrissie1)
type: post
date: 2009-10-21T05:33:13+00:00
ID: 592
url: /index.php/desktopdev/mstech/application-startuppath-and-nunit/
views:
  - 4790
categories:
  - Microsoft Technologies
tags:
  - application.startuppath
  - nunit
  - vb.net

---
In some cases, you want to have a file in your Startupfolder that you want to access from within your application. Perhaps an XML-file with application settings or an image that can be changed. 

In most cases the application startuppath will be in program files so you don&#8217;t really want to put too much stuff in there but from time to time, you might. 

And I always like to test if the pesky file is configured right, so that it appears as a file in that folder.

For example, if I have an image that has this as a path:

```vbnet
Public Class Logo 
  Private _Path as string
  Private _Image as Image

  Public Sub New()
    _Path = Application.StartupPath + "image.jpg"
  End Sub

  Public Property Image as Image ...
End Class
```
And you then make a test like this:

```vbnet
[Test]
Public Sub Image_Should_Be_Available_In_Application_StartupPath()  
  Dim _Logo as new Logo
Assert.IsTrue(System.IO.File.Exists(_Logo.Path))
End Sub ```
It will fail because it can not find the file. This is because most unittestrunners take a shadow copy of your assembly. So if you do an application.startupPath, you will end up in the Unittestrunners folder and not the one with your assembly in it. Turning the shadow copy thing off did not resolve the issue for me.

In my case I resolved it by doing this:

```vbnet
[Test]
Public Sub Image_Should_Be_Available_In_Application_StartupPath()  
  Dim _Logo as new Logo
Assert.IsTrue(System.IO.File.Exists(_Logo.Path.Replace(Application.StartupPath & "","")))
End Sub ```
That worked for me. Hope it works for you. Not something you would use a lot but still, perhaps someday.