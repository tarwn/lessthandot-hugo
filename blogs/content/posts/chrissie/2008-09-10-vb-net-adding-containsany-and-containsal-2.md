---
title: 'VB.Net: Adding ContainsAny and ContainsAll to ICollection(of T) part 2'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-10T07:10:35+00:00
ID: 135
url: /index.php/desktopdev/mstech/vb-net-adding-containsany-and-containsal-2/
views:
  - 2709
categories:
  - Microsoft Technologies
tags:
  - extension method
  - ienumberable
  - vb.net

---
[Since writing the last post on this][1], I refined these methods a bit.

```vbnet
Imports System.Runtime.CompilerServices

Namespace Extensions
    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Public Module IEnumerableExtensions

        ''' &lt;summary&gt;
        ''' Sees if the collection contains any of the parameters. If it finds one then it will return true else it will return false.
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="T"&gt;any object&lt;/typeparam&gt;
        ''' &lt;param name="Collection"&gt;an IEnumerable of T&lt;/param&gt;
        ''' &lt;param name="Parameters"&gt;an IEnumerable of T&lt;/param&gt;
        ''' &lt;returns&gt;True if at least one found, false if not found&lt;/returns&gt;
        ''' &lt;remarks&gt;This method equals the OR operator for all elements in Parameter collection. So if Collection contains parameter1 OR parameter2 OR parametern then it will return true.&lt;/remarks&gt;
        &lt;Extension()&gt; _
        Public Function ContainsAny(Of T)(ByVal Collection As IEnumerable(Of T), ByVal Parameters As IEnumerable(Of T)) As Boolean
            Return Collection.ContainsAny(Parameters.ToArray)
        End Function

        ''' &lt;summary&gt;
        ''' Sees if the collection contains all of the parameters. if it finds all then it will return true else it will return false.
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="T"&gt;any object&lt;/typeparam&gt;
        ''' &lt;param name="Collection"&gt;an IEnumerable of T&lt;/param&gt;
        ''' &lt;param name="Parameters"&gt;an IEnumerable of T&lt;/param&gt;
        ''' &lt;returns&gt;True if all found, false if one not found&lt;/returns&gt;
        ''' &lt;remarks&gt;This method equals the AND operator for all elements in Parameter collection. So if Collection contains parameter1 AND parameter2 AND parametern then it will return true.&lt;/remarks&gt;
        &lt;Extension()&gt; _
        Public Function ContainsAll(Of T)(ByVal Collection As IEnumerable(Of T), ByVal Parameters As IEnumerable(Of T)) As Boolean
            Return Collection.ContainsAll(Parameters.ToArray)
        End Function

        ''' &lt;summary&gt;
        ''' Sees if the collection contains any of the parameters. If it finds one then it will return true else it will return false.
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="T"&gt;any object&lt;/typeparam&gt;
        ''' &lt;param name="Collection"&gt;an array of T&lt;/param&gt;
        ''' &lt;param name="Parameters"&gt;an array of T&lt;/param&gt;
        ''' &lt;returns&gt;True if at least one found, false if not found&lt;/returns&gt;
        ''' &lt;remarks&gt;This method equals the OR operator for all elements in Parameter collection. So if Collection contains parameter1 OR parameter2 OR parametern then it will return true.&lt;/remarks&gt;
        &lt;Extension()&gt; _
        Public Function ContainsAny(Of T)(ByVal Collection As IEnumerable(Of T), ByVal Parameters As T()) As Boolean
            For Each Parameter As T In Parameters
                If Collection.Contains(Parameter) Then
                    Return True
                End If
            Next
            Return False
        End Function

        ''' &lt;summary&gt;
        ''' Sees if the collection contains all of the parameters. if it finds all then it will return true else it will return false.
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="T"&gt;any object&lt;/typeparam&gt;
        ''' &lt;param name="Collection"&gt;an array of T&lt;/param&gt;
        ''' &lt;param name="Parameters"&gt;an array of T&lt;/param&gt;
        ''' &lt;returns&gt;True if all found, false if one not found&lt;/returns&gt;
        ''' &lt;remarks&gt;This method equals the AND opearator for all elements in Parameter collection. So if Collection contains parameter1 AND parameter2 AND parametern then it will return true.&lt;/remarks&gt;
        &lt;Extension()&gt; _
        Public Function ContainsAll(Of T)(ByVal Collection As IEnumerable(Of T), ByVal Parameters As T()) As Boolean
            For Each Parameter As T In Parameters
                If Not Collection.Contains(Parameter) Then
                    Return False
                End If
            Next
            Return True
        End Function
    End Module
End Namespace```
This one now takes IEnumerable as the type (since ICollection inherits from IEnumerable it just broadens the scope) and it takes an IEnumerable or an array as parameters. Seems to do everything I want it to do right know. 

And how do I know that? Because of 24 Unittests I wrote. I won&#8217;t post them here, but I have them on the [wiki][2].

 [1]: /index.php/DesktopDev/MSTech/vb-net-adding-containsany-and-containsal
 [2]: http://wiki.ltd.local/index.php/VB.Net:_ContainsAny_and_ContainsAll_extension_methods