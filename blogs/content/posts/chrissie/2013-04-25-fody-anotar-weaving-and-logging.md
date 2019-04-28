---
title: Fody Anotar weaving and logging combined into one package.
author: Christiaan Baes (chrissie1)
type: post
date: 2013-04-25T06:53:00+00:00
ID: 2072
excerpt: Trying out Anotar Fody.
url: /index.php/desktopdev/mstech/fody-anotar-weaving-and-logging/
views:
  - 9267
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - anotar
  - fody
  - nlog

---
## Introduction

I said it once and I will say it again. Logging is very important once your app goes live. It&#8217;s the best way for you to find out what the user did to destroy your sanity.
  
I am always on the lookout for new and exiting things that will make logging easier for me. I want less code but maximum information.
  
You can get lots of information with [Gibraltar&#8217;s Loupe][1] but you can get even less code and more information with [Anotar][2].

## The code

It&#8217;s easy.

Just add the Anotar.Nlog package from nuget and then add some config for nlog. Like this.

```xml
&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;configuration&gt;
  &lt;configSections&gt;
    &lt;section name="nlog" type="NLog.Config.ConfigSectionHandler, NLog"/&gt;
  &lt;/configSections&gt;
  &lt;startup&gt;
    &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5"/&gt;
  &lt;/startup&gt;
  &lt;system.serviceModel&gt;
    &lt;bindings/&gt;
    &lt;client/&gt;
  &lt;/system.serviceModel&gt;
  &lt;nlog&gt;
    &lt;targets&gt;
      &lt;target name="file" type="File" fileName="_${level}.log" layout="${longdate} ${threadid} [${level:uppercase=true}] ${message}" archiveFileName="_${level}.{#}.log" archiveAboveSize="1048576" archiveNumbering="Sequence" maxArchiveFiles="10" concurrentWrites="true" keepFileOpen="false" encoding="iso-8859-2"/&gt;
    &lt;/targets&gt;
    &lt;rules&gt;
      &lt;logger name="*" minlevel="Debug" writeTo="file"/&gt;
    &lt;/rules&gt;
  &lt;/nlog&gt;
&lt;/configuration&gt;```
And try it with this code.

```vbnet
Module Module1

    Sub Main()
        Dim l As New Logthing
        l.TestSub()
        Console.WriteLine(l.TestFunction)
        Anotar.NLog.Log.Info("Some info in a static method")
    End Sub

    Public Class Logthing
        Public Sub TestSub()
            Anotar.NLog.Log.Debug("My message for sub")
        End Sub

        Public Function TestFunction() As String
            Anotar.NLog.Log.Debug("My message for function")
            Return "This thing"
        End Function

    End Class

End Module
```
This is then the resulting debug log file.

> 2013-04-25 13:24:00.1197 8 [DEBUG] Method: &#8216;System.Void ConsoleApplication1.Module1/Logthing::TestSub()&#8217;. Line: ~12. My message for sub
  
> 2013-04-25 13:24:00.1387 8 [DEBUG] Method: &#8216;System.String ConsoleApplication1.Module1/Logthing::TestFunction()&#8217;. Line: ~16. My message for function

And the info file.

> 2013-04-25 13:24:00.1387 8 [INFO] Method: &#8216;System.Void ConsoleApplication1.Module1::Main()&#8217;. Line: ~7. Some info in a static method

The lines seem to be correct and the other information too. 

But how does Fody do it?

Well, it changes your code at compile time. 

This for instance is the LogThing class after a decompile by JustDecompile.

```vbnet
Public Class Logthing
    Private Shared AnotarLogger As Logger

    Shared Sub New()
        Logthing.AnotarLogger = LogManager.GetLogger("ConsoleApplication1.Module1/Logthing")
    End Sub

    &lt;DebuggerNonUserCode&gt; _
    Public Sub New()
        MyBase.New()
    End Sub

    Public Function TestFunction() As String 
        Dim V_2 As Object() = New Object(0)
        Dim V_1 As String = "My message for function"
        V_1 = String.Concat("Method: 'System.String ConsoleApplication1.Module1/Logthing::TestFunction()'. Line: ~16. ", V_1)
        Logthing.AnotarLogger.Debug(V_1, V_2)
        Dim TestFunction As String = "This thing"
        Return TestFunction
    End Function

    Public Sub TestSub()
        Dim V_1 As Object() = New Object(0)
        Dim V_0 As String = "My message for sub"
        V_0 = String.Concat("Method: 'System.Void ConsoleApplication1.Module1/Logthing::TestSub()'. Line: ~12. ", V_0)
        Logthing.AnotarLogger.Debug(V_0, V_1)
    End Sub
End Class```
As you can see it is not doing any reflection in your code. It just replaces your Log.Debug code with other code. Which means it should be faster at runtime. 

This also means that compiling will be slower, because it has to change your code. How much? No idea. 

## Conclusion

I like this.

 [1]: /index.php/All/?p=2162
 [2]: https://github.com/Fody/Anotar