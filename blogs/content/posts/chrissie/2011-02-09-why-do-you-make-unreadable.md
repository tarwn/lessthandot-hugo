---
title: Why do you make unreadable logfiles?
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-09T08:06:00+00:00
ID: 1034
excerpt: |
  Why do you make unreadable logfiles? I was talking to myself there, me and myself tend to have such conversations a lot lately. Anyway, we digress.
  
  I used to have some simple output for my logfiles.
  
  [Header started logging at 3/02/2011 13:20:38]&hellip;
url: /index.php/desktopdev/mstech/why-do-you-make-unreadable/
views:
  - 5932
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - htmllayout
  - log4net
  - skeletonlayout

---
Why do you make unreadable logfiles? I was talking to myself there, me and myself tend to have such conversations a lot lately. Anyway, we digress.

I used to have some simple output for my logfiles.

```
[Header started logging at 3/02/2011 13:20:38]
2011-02-03 13:20:48,003 [1] WARN  Logger [TDB2009.View.Menu.Winforms.Startup.StartUp] - Is this warning on?
[footer]
```
If you put that in debugging mode you will end up with a lot of files and not much of it will be readable. Finding the warns will be difficult at best. 

I can do two things to improve on that. 

1. Write a parser and make it pretty.
  
2. Download a parser that does it for me.
  
3. Make it HTML.

I choose to make it HTML. Not the perfect solution, but OK for now.

I used a coded solution since I don&#8217;t think these things need to change at runtime anyway, it&#8217;s a client application, so the only thing that really needs to change at runtime might be the loglevel.

```vbnet
Layout.Header = "&lt;!DOCTYPE html&gt;"
			Layout.Header &= "&lt;html lang=""en""&gt;"
			Layout.Header &= "&lt;head&gt;"
			Layout.Header &= "&lt;meta charset=""utf-8"" /&gt;"
			Layout.Header &= "&lt;title&gt;LogFile&lt;/title&gt;"
			Layout.Header &= "&lt;style type=""text/css""&gt;"
			Layout.Header &= "table{border-collapse:collapse;width:100%; }"
			Layout.Header &= "table, th, td{ border: 1px solid black;}"
			Layout.Header &= "th{background-color:gray; color:white;}"
			Layout.Header &= ".WARN{color:red;}"
			Layout.Header &= ".DEBUG{color:green;}"
			Layout.Header &= "&lt;/style&gt;"
			Layout.Header &= "&lt;/head&gt;"
			Layout.Header &= "&lt;body&gt;"
			Layout.Header &= "&lt;p&gt;Started logging at " & DateTime.Now & "&lt;/p&gt;"
			Layout.Header &= "&lt;table&gt;"
			Layout.Header &= "&lt;tr&gt;&lt;th style=""width:200px;""&gt;Date and time&lt;/th&gt;&lt;th style=""width:100px;""&gt;Thread&lt;/th&gt;&lt;th style=""width:100px;""&gt;Level&lt;/th&gt;&lt;th style=""width:100px;""&gt;Logger&lt;/th&gt;&lt;th style=""width:200px;""&gt;Class&lt;/th&gt;&lt;th&gt;Message&lt;/th&gt;&lt;/tr&gt;"
			Layout.ConversionPattern = "&lt;tr class=""%-5level""&gt;&lt;td&gt;%date&lt;/td&gt;&lt;td&gt;%thread&lt;/td&gt;&lt;td&gt;%-5level&lt;/td&gt;&lt;td&gt;%logger&lt;/td&gt;&lt;td&gt; %class &lt;/td&gt;&lt;td&gt;%message&lt;/td&gt;&lt;/tr&gt;"
			Layout.Footer = "&lt;/table&gt;"
			Layout.Footer &= "&lt;p&gt;Stopped logging&lt;/p&gt;"
			Layout.Footer &= "&lt;hr /&gt;"
			Layout.Footer &= "&lt;/body&gt;&lt;/html&gt;"```
With this being the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/log4net/log4net1.png?mtime=1297180388"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/log4net/log4net1.png?mtime=1297180388" width="1001" height="418" /></a>
</div>

I will need to check what the result will be if the file is split up over multiple files since it won&#8217;t see the css anymore. It won&#8217;t slow things down too much since the conversion pattern is only slightly bigger, I might want to make info green and debug black too.

The above worksjust fine, but fine isn&#8217;t good enough, so I decide to do better and create my own LayoutClass.

Making your own LayoutClass is easy. You just have to inherit LayoutSkeleton and implement some methods ;-).

And here it is.

```vbnet
Imports log4net.Layout
Imports System.Text

Namespace Logging.FileLog
	Public Class HtmlLayout
		Inherits LayoutSkeleton

		Private Shared AlternateColor As Boolean = True

		Public Sub New()
			IgnoresException = True
		End Sub

		Public Overrides Sub ActivateOptions()

		End Sub

		Public Overrides Sub Format(ByVal writer As System.IO.TextWriter, ByVal loggingEvent As log4net.Core.LoggingEvent)
			If loggingEvent Is Nothing Then
				Throw New ArgumentNullException("loggingEvent")
			End If
			If AlternateColor Then
				writer.Write("&lt;tr class=""" & loggingEvent.Level.ToString.ToLower & """&gt;")
			Else
				writer.Write("&lt;tr class=""" & loggingEvent.Level.ToString.ToLower & " alternatecolor""&gt;")
			End If
			AlternateColor = Not AlternateColor
			writer.Write("&lt;td&gt;" & DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss") & "&lt;/td&gt;")
			writer.Write("&lt;td&gt;" & loggingEvent.ThreadName & "&lt;/td&gt;")
			writer.Write("&lt;td&gt;" & loggingEvent.Level.DisplayName & "&lt;/td&gt;")
			If loggingEvent.ExceptionObject IsNot Nothing Then
				writer.Write("&lt;td&gt;" & loggingEvent.ExceptionObject.Source & "&lt;/td&gt;")
			Else
				writer.Write("&lt;td&gt;?&lt;/td&gt;")
			End If
			writer.Write("&lt;td&gt;")
			loggingEvent.WriteRenderedMessage(writer)
			writer.Write("&lt;/td&gt;&lt;/tr&gt;")
			writer.WriteLine()
		End Sub

		Public Overrides Property Header As String
			Get
				Dim _Header As New StringBuilder
				_Header.Append("&lt;!DOCTYPE html&gt;")
				_Header.Append("&lt;html lang=""en""&gt;")
				_Header.Append("&lt;head&gt;")
				_Header.Append("&lt;meta charset=""utf-8"" /&gt;")
				_Header.Append("&lt;title&gt;LogFile&lt;/title&gt;")
				_Header.Append("&lt;style type=""text/css""&gt;")
				_Header.Append("table, th, td{ border: 1px solid black;}")
				_Header.Append("th{background-color:gray; color:white;}")
				_Header.Append(".warn{color:red;}")
				_Header.Append(".info{color:green;}")
				_Header.Append(".alternatecolor{background-color:lightCyan;}")
				_Header.Append("&lt;/style&gt;")
				_Header.Append("&lt;/head&gt;")
				_Header.Append("&lt;body&gt;")
				_Header.Append("&lt;p&gt;Started logging at " & DateTime.Now & "&lt;/p&gt;")
				_Header.Append("&lt;table style=""border-collapse:collapse;width:100%;border: 1px solid black;""&gt;")
				_Header.Append("&lt;tr&gt;&lt;th style=""width:200px;""&gt;Date and time&lt;/th&gt;&lt;th style=""width:100px;""&gt;Thread&lt;/th&gt;&lt;th style=""width:100px;""&gt;Level&lt;/th&gt;&lt;th style=""width:200px;""&gt;Class&lt;/th&gt;&lt;th&gt;Message&lt;/th&gt;&lt;/tr&gt;")
				Return _Header.ToString
			End Get
			Set(ByVal value As String)
				MyBase.Header = value
			End Set
		End Property

		Public Overrides Property Footer As String
			Get
				Dim _Footer As New StringBuilder
				_Footer.Append("&lt;/table&gt;")
				_Footer.Append("&lt;p&gt;Stopped logging at " & DateTime.Now & "&lt;/p&gt;")
				_Footer.Append("&lt;hr /&gt;")
				_Footer.Append("&lt;/body&gt;&lt;/html&gt;")
				Return _Footer.ToString
			End Get
			Set(ByVal value As String)
				MyBase.Footer = value
			End Set
		End Property
	End Class
End Namespace```
Oh my I used a static variable, but no harm was done.
  
And here is the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/log4net/log4net2.png?mtime=1297245330"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/log4net/log4net2.png?mtime=1297245330" width="1006" height="287" /></a>
</div>

Kinda much more readable. And very easy to implement.
  
When I now create an appender I just have to give it this layout.

```vbnet
Dim _RollingFileAppender = new RollingFileAppender()
' Do the config jig
_RollingFileAppender.Layout = new HTMLLayout()
_RollingFileAppender.ActivateOptions()
```
When it starts a new file it will print the footer in the last file and print a header in the new file.

Did you notice how I now have the datetime in the footer. If you do this with the patternlayout it will not do this correctly because the footerproperty is set at config time not at runtime.