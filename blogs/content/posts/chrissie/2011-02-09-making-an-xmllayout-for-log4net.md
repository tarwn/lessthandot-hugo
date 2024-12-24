---
title: Making an XMLLayout for Log4Net with an xsl file to make it human readable.
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-09T11:48:00+00:00
ID: 1037
excerpt: |
  In one of my previous posts where I made an HTMLLayout for Log4Net Wim ten Brink commented that I should use XML with xsl instead.
  
  AS I always listen to my followers. I did so swiftly. Here is the XslLayout class I created, Yes I know that there is a&hellip;
url: /index.php/desktopdev/mstech/making-an-xmllayout-for-log4net/
views:
  - 7536
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - log4net
  - vb.net
  - xsllayout

---
In one of my [previous posts][1] where I made an HTMLLayout for Log4Net Wim ten Brink commented that I should use XML with xsl instead.

AS I always listen to my followers. I did so swiftly. Here is the XslLayout class I created, Yes I know that there is an XMLLayout class available but that isn&#8217;t as good as the one I have.

```vbnet
Imports log4net.Layout
Imports System.Text

Namespace Logging.FileLog
	Public Class XslLayout
		Inherits LayoutSkeleton
		
		Public Overrides Sub ActivateOptions()

		End Sub

		Public Overrides Sub Format(ByVal writer As System.IO.TextWriter, ByVal loggingEvent As log4net.Core.LoggingEvent)
			If loggingEvent Is Nothing Then
				Throw New ArgumentNullException("loggingEvent")
			End If
			writer.Write("&lt;log&gt;")
			writer.Write("&lt;datetime&gt;" & DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss") & "&lt;/datetime&gt;")
			writer.Write("&lt;thread&gt;" & loggingEvent.ThreadName & "&lt;/thread&gt;")
			writer.Write("&lt;level&gt;" & loggingEvent.Level.DisplayName & "&lt;/level&gt;")
			If loggingEvent.ExceptionObject IsNot Nothing Then
				writer.Write("&lt;class&gt;" & loggingEvent.ExceptionObject.Source & "&lt;/class&gt;")
			Else
				writer.Write("&lt;class&gt;?&lt;/class&gt;")
			End If
			writer.Write("&lt;message&gt;")
			loggingEvent.WriteRenderedMessage(writer)
			writer.Write("&lt;/message&gt;")
			writer.Write("&lt;/log&gt;")
			writer.WriteLine()
		End Sub

		Public Overrides Property Header As String
			Get
				Dim _Header As New StringBuilder
				_Header.Append("&lt;?xml version=""1.0"" encoding=""utf-8""?&gt;")
				_Header.Append("&lt;?xml-stylesheet href=""log.xsl"" type=""text/xsl""?&gt;")
				_Header.Append("&lt;logs&gt;")
				_Header.Append("&lt;header&gt;Started logging at " & DateTime.Now & "&lt;/header&gt;")
				Return _Header.ToString
			End Get
			Set(ByVal value As String)
				MyBase.Header = value
			End Set
		End Property

		Public Overrides Property Footer As String
			Get
				Dim _Footer As New StringBuilder
				_Footer.Append("&lt;footer&gt;Stopped logging at " & DateTime.Now & "&lt;/footer&gt;")
				_Footer.Append("&lt;/logs&gt;")
				Return _Footer.ToString
			End Get
			Set(ByVal value As String)
				MyBase.Footer = value
			End Set
		End Property
	End Class
End Namespace```
and this is the xsl file to go with it.

```xml
&lt;?xml version="1.0"?&gt;

&lt;xsl:stylesheet version="1.0"
xmlns:xsl="http://www.w3.org/1999/XSL/Transform"&gt;

  &lt;xsl:template match="/"&gt;
    &lt;html lang="en"&gt;
      &lt;head&gt;
        &lt;meta charset="utf-8" /&gt;
        &lt;title&gt;LogFile&lt;/title&gt;
        &lt;style type="text/css"&gt;
          table, th, td{ border: 1px solid black;}
          table {border-collapse:collapse;width:100%;}
          th{background-color:gray; color:white;}
          .warn{color:red;}
          .info{color:green;}
          .alternatecolor{background-color:lightCyan;}
        &lt;/style&gt;
      &lt;/head&gt;
      &lt;body&gt;
          &lt;p&gt;
            &lt;xsl:value-of select="logs/header"/&gt;
          &lt;/p&gt;
          &lt;table&gt;
            &lt;tr&gt;
              &lt;th&gt;Date time&lt;/th&gt;
              &lt;th&gt;Thread&lt;/th&gt;
              &lt;th&gt;Level&lt;/th&gt;
              &lt;th&gt;Class&lt;/th&gt;
              &lt;th&gt;Message&lt;/th&gt;
            &lt;/tr&gt;
            &lt;xsl:for-each select="logs/log"&gt;
              &lt;tr&gt;
                &lt;xsl:if test="position() mod 2 = 1"&gt;
                  &lt;xsl:attribute name="class"&gt;
                    &lt;xsl:value-of select="translate(level, 'ABCDEFGHIJKLMNOPQRSTUVWXYZ ', 'abcdefghijklmnopqrstuvwxyz_')"/&gt;
                  &lt;/xsl:attribute&gt;
                &lt;/xsl:if&gt;
                &lt;xsl:if test="position() mod 2 = 0"&gt;
                  &lt;xsl:attribute name="class"&gt;
                    &lt;xsl:value-of select="translate(level, 'ABCDEFGHIJKLMNOPQRSTUVWXYZ ', 'abcdefghijklmnopqrstuvwxyz_')"/&gt;
                    alternatecolor
                  &lt;/xsl:attribute&gt;
                &lt;/xsl:if&gt;
                &lt;td&gt;
                  &lt;xsl:value-of select="datetime"/&gt;
                &lt;/td&gt;
                &lt;td&gt;
                  &lt;xsl:value-of select="level"/&gt;
                &lt;/td&gt;
                &lt;td&gt;
                  &lt;xsl:value-of select="thread"/&gt;
                &lt;/td&gt;
                &lt;td&gt;
                  &lt;xsl:value-of select="class"/&gt;
                &lt;/td&gt;
                &lt;td&gt;
                  &lt;xsl:value-of select="message"/&gt;
                &lt;/td&gt;
              &lt;/tr&gt;
            &lt;/xsl:for-each&gt;
          &lt;/table&gt;
          &lt;p&gt;
            &lt;xsl:value-of select="logs/footer"/&gt;
          &lt;/p&gt;
        &lt;hr /&gt;
      &lt;/body&gt;
    &lt;/html&gt;
  &lt;/xsl:template&gt;

&lt;/xsl:stylesheet&gt;```
That was fun.

And here the result.

The XML logfile.

```xml
&lt;?xml version="1.0" encoding="utf-8"?&gt;&lt;?xml-stylesheet href="log.xsl" type="text/xsl"?&gt;&lt;logs&gt;&lt;header&gt;Started logging at 9/02/2011 14:27:11&lt;/header&gt;&lt;log&gt;&lt;datetime&gt;2011-02-09 14:27:16&lt;/datetime&gt;&lt;thread&gt;10&lt;/thread&gt;&lt;level&gt;INFO&lt;/level&gt;&lt;class&gt;?&lt;/class&gt;&lt;message&gt;Using NHibernateConfiguration. Database:  Server: &lt;/message&gt;&lt;/log&gt;
&lt;log&gt;&lt;datetime&gt;2011-02-09 14:27:18&lt;/datetime&gt;&lt;thread&gt;10&lt;/thread&gt;&lt;level&gt;DEBUG&lt;/level&gt;&lt;class&gt;?&lt;/class&gt;&lt;message&gt;Constructor - Log initialized&lt;/message&gt;&lt;/log&gt;
&lt;log&gt;&lt;datetime&gt;2011-02-09 14:27:19&lt;/datetime&gt;&lt;thread&gt;10&lt;/thread&gt;&lt;level&gt;WARN&lt;/level&gt;&lt;class&gt;?&lt;/class&gt;&lt;message&gt;Is this warning on?&lt;/message&gt;&lt;/log&gt;
&lt;log&gt;&lt;datetime&gt;2011-02-09 14:27:19&lt;/datetime&gt;&lt;thread&gt;10&lt;/thread&gt;&lt;level&gt;DEBUG&lt;/level&gt;&lt;class&gt;?&lt;/class&gt;&lt;message&gt;Is this debug on?&lt;/message&gt;&lt;/log&gt;
&lt;log&gt;&lt;datetime&gt;2011-02-09 14:27:19&lt;/datetime&gt;&lt;thread&gt;10&lt;/thread&gt;&lt;level&gt;INFO&lt;/level&gt;&lt;class&gt;?&lt;/class&gt;&lt;message&gt;Is this info on?&lt;/message&gt;&lt;/log&gt;
&lt;footer&gt;Stopped logging at 9/02/2011 14:27:22&lt;/footer&gt;&lt;/logs&gt;```
And the result in the browser.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/log4net/log4net2.png?mtime=1297245330"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/log4net/log4net2.png?mtime=1297245330" width="1006" height="287" /></a>
</div>

That looks exactly the same as previous and the resulting logfile is machinereadable to boot.

<span class="MT_red">Warning: Your appender should have the AppendToFile attribute set to False else this will generate invalid XML. That is also the biggest drawback of this method. You could of course just create invalid XML and then write a parser but that was not really the point of the exercise.</p> 

<p>
  Warnign 2: You also want to write the xsl to where the log file has been written else the result will be less satisfying.</span>
</p>

 [1]: /index.php/DesktopDev/MSTech/why-do-you-make-unreadable