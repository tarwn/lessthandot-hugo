---
title: Json.Net ‘s JObject to dynamic in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-11T12:44:00+00:00
ID: 1786
excerpt: Why a Json.net is not really dynamic in VB.Net and how you can use Jsonfx to work around that.
url: /index.php/desktopdev/mstech/json-net-s-jobject-to/
views:
  - 8409
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - json.net
  - jsonfx
  - signalr
  - vb.net

---
In my [previous post][1] I showed you how to get an object from a call to a SignalR method.

That call on the client side returns a [Json.Net][2] JObject. This is dynamic object.

However, the implementation of this object is not very VB.Net friendly. 

If you try this.

```vbnet
Imports System.Runtime.CompilerServices
Imports Newtonsoft.Json
Imports Newtonsoft.Json.Linq
Imports Microsoft.AspNet.SignalR.Client.Hubs

Module Module1

    Sub Main()
        Dim connection = New HubConnection("http://localhost:50865")

        Dim plants = connection.CreateHubProxy("Plants")

        plants.On(Of String)("addMessage", Sub(data) Console.WriteLine(data.ToString()))

        connection.Start().Wait()

        Dim line As String = Nothing
        Console.WriteLine("type the id + enter to send, type exit + enter to exit.")
        While line &lt;&gt; "exit"
            line = Console.ReadLine()
            plants.Invoke(Of Object)("GetPlant", line).ContinueWith(Sub(data)
                                                                        If data.Result IsNot Nothing Then
                                                                            Console.WriteLine(data.Result.ToString)
                                                                            Dim result = data.Result
                                                                            Console.WriteLine(result.Id)
                                                                            Console.WriteLine(result.Genus)
                                                                            Console.WriteLine(result.Species)
                                                                            For Each commonName In result.CommonNames
                                                                                Console.WriteLine(commonName)
                                                                            Next
                                                                        Else
                                                                            Console.WriteLine("No plant with that id.")
                                                                        End If
                                                                    End Sub)
        End While
    End Sub

End Module
```
You will get an exception on result.Id

> System.MissingMemberException was unhandled by user code
    
> HResult=-2146233070
    
> Message=Public member &#8216;Id&#8217; on type &#8216;JObject&#8217; not found.
    
> Source=Microsoft.VisualBasic
    
> StackTrace:
         
> at Microsoft.VisualBasic.CompilerServices.Symbols.Container.GetMembers(String& MemberName, Boolean ReportErrors)
         
> at Microsoft.VisualBasic.CompilerServices.NewLateBinding.ObjectLateGet(Object Instance, Type Type, String MemberName, Object[] Arguments, String[] ArgumentNames, Type[] TypeArguments, Boolean[] CopyBack)
         
> at Microsoft.VisualBasic.CompilerServices.NewLateBinding.FallbackGet(Object Instance, String MemberName, Object[] Arguments, String[] ArgumentNames)
         
> at CallSite.Target(Closure , CallSite , Object )
         
> at System.Dynamic.UpdateDelegates.UpdateAndExecute1\[T0,TRet\](CallSite site, T0 arg0)
         
> at Invoker(CallSiteBinder , Object , Object[] )
         
> at Microsoft.VisualBasic.CompilerServices.IDOUtils.CreateRefCallSiteAndInvoke(CallSiteBinder Action, Object Instance, Object[] Arguments)
         
> at Microsoft.VisualBasic.CompilerServices.IDOBinder.IDOGet(IDynamicMetaObjectProvider Instance, String MemberName, Object[] Arguments, String[] ArgumentNames, Boolean[] CopyBack)
         
> at Microsoft.VisualBasic.CompilerServices.NewLateBinding.LateGet(Object Instance, Type Type, String MemberName, Object[] Arguments, String[] ArgumentNames, Type[] TypeArguments, Boolean[] CopyBack)
         
> at SignalRConsole.Module1.\_Lambda$\__2(Task\`1 data) in E:UserschristiaanDocumentsVisual Studio 2012ProjectsSignalRTestingSignalRConsoleModule1.vb:line 25
         
> at System.Threading.Tasks.ContinuationTaskFromResultTask\`1.InnerInvoke()
         
> at System.Threading.Tasks.Task.Execute()
    
> InnerException: 

The problem is the LateGet. But you can found out all about why this is a problem for VB.Net in the [following post][3] by Matthew Doig.

And none of the Json.net converters can solve this for us. 

But we can use another library that does work with VB.Net.

And it&#8217;s called [JSonFX][4]. Just add it to your project via Nuget and then we can change our code to this.

```vbnet
Imports System.Runtime.CompilerServices
Imports Newtonsoft.Json
Imports Newtonsoft.Json.Linq
Imports Microsoft.AspNet.SignalR.Client.Hubs

Module Module1

    Sub Main()
        Dim connection = New HubConnection("http://localhost:50865")

        Dim plants = connection.CreateHubProxy("Plants")

        plants.On(Of String)("addMessage", Sub(data) Console.WriteLine(data.ToString()))

        connection.Start().Wait()

        Dim line As String = Nothing
        Console.WriteLine("type the id + enter to send, type exit + enter to exit.")
        While line &lt;&gt; "exit"
            line = Console.ReadLine()
            plants.Invoke(Of Object)("GetPlant", line).ContinueWith(Sub(data)
                                                                        If data.Result IsNot Nothing Then
                                                                            Console.WriteLine(data.Result.ToString)
                                                                            Dim reader = New JsonFx.Json.JsonReader()
                                                                            Dim result = reader.Read(data.Result.ToString)
                                                                            Console.WriteLine(result.Id)
                                                                            Console.WriteLine(result.Genus)
                                                                            Console.WriteLine(result.Species)
                                                                            For Each commonName In result.CommonNames
                                                                                Console.WriteLine(commonName)
                                                                            Next
                                                                        Else
                                                                            Console.WriteLine("No plant with that id.")
                                                                        End If
                                                                    End Sub)
        End While
    End Sub

End Module
```
Now our result will be.

> type the id + enter to send, type exit + enter to exit.
  
> 1
  
> {
    
> &#8220;Id&#8221;: 1,
    
> &#8220;Genus&#8221;: &#8220;Fagus&#8221;,
    
> &#8220;Species&#8221;: &#8220;Sylvatica&#8221;,
    
> &#8220;CommonNames&#8221;: [
      
> &#8220;Common Beech&#8221;
    
> ]
  
> }
  
> Someone requested id: 1
  
> 1
  
> Fagus
  
> Sylvatica
  
> Common Beech

Which is ultra cool.

 [1]: /index.php/WebDev/ServerProgramming/signalr-and-vb-net-hubs
 [2]: http://json.codeplex.com/
 [3]: http://www.broadcastbabble.com/why-vb-net-needs-a-dynamic-keyword/
 [4]: http://www.jsonfx.net/