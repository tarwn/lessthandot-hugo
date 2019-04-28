---
title: 'Visual Studio: Working with the designer'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-08-02T13:54:43+00:00
ID: 99
url: /index.php/desktopdev/mstech/visual-studio-working-with-the-designer/
views:
  - 2001
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET

---
We all know by now that the designer uses the empty constructor to show the form on the screen and the usercontrols. But you want to make a constructor to inject your presenter and you want everyone to use that constructor. And perhaps you want to do some other things in the constructor. But you know this will make the designer choke on your form and never show it again. Now you could just write all the code by hand from then on in&#8230; or not.

Just keep the empty/default constructor and make it obsolete and make it error so no one can use it ever again, except the designer because he doesn&#8217;t seem to care.

This is the C# way.

```csharp
public class Class1
    {
        
        [Obsolete("reason", true)]
        public void Class1()
        {
        }
    }```
and this the VB.Net way

```vbnet
Public Class Class1

    &lt;Obsolete("message", True)&gt; _
    Public Sub New()

    End Sub

End Class
```
I know it isn&#8217;t pretty but it solves a problem in a more or less decent way. Let&#8217;s call it a workaround.