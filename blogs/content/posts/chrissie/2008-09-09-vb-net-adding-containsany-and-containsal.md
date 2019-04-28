---
title: 'VB.Net: Adding ContainsAny and ContainsAll to ICollection(of T)'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-09T07:52:08+00:00
ID: 134
url: /index.php/desktopdev/mstech/vb-net-adding-containsany-and-containsal/
views:
  - 4140
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - extension methods
  - linq
  - vb.net

---
In an ICollection(of T) you have a contains method to see if your collection has the requested element in it. But if you want to look for Multiple elements, you have to resort to making predicates (and we all know how ugly those get). 

So why didn&#8217;t MS implement ContainsAny (OR) and/or ContainsAll (AND). I couldn&#8217;t think of a good reason, so I made them myself. They are perhaps a bit over easy and not very performance friendly but they work.

Here are the extension methods.

```vbnet
&lt;Extension()&gt; _
    Public Function ContainsAny(Of T)(ByVal collection As ICollection(Of T), ByVal Parameters As ICollection(Of T)) As Boolean
        For Each parameter In Parameters
            If collection.Contains(parameter) Then
                Return True
            End If
        Next
        Return False
    End Function

    &lt;Extension()&gt; _
    Public Function ContainsAll(Of T)(ByVal collection As ICollection(Of T), ByVal Parameters As ICollection(Of T)) As Boolean
        For Each parameter In Parameters
            If Not collection.Contains(parameter) Then
                Return False
            End If
        Next
        Return True
    End Function```
They are generic, so you can use them in most places.

Here is an example how it works.

```vbnet
Imports System.Runtime.CompilerServices
Imports System.Net.NetworkInformation

Module Module1

    Sub Main()
        ShowNetworkInterfaces()
        Console.ReadLine()
    End Sub

    Public Sub ShowNetworkInterfaces()
        Dim computerProperties As IPGlobalProperties = IPGlobalProperties.GetIPGlobalProperties()
        Dim nics As NetworkInterface() = NetworkInterface.GetAllNetworkInterfaces()
        If nics Is Nothing OrElse nics.Length &lt; 1 Then
            Console.WriteLine("  No network interfaces found.")
            Exit Sub
        End If
        For Each adapter As NetworkInterface In nics
            Dim properties As IPInterfaceProperties = adapter.GetIPProperties()
            Console.WriteLine(adapter.Name)
            For Each ip As System.Net.IPAddress In properties.DnsAddresses
                Console.WriteLine("DNS server : {0}", ip.ToString)
            Next
        Next
        Dim _list As New List(Of System.Net.IPAddress)
        _list.Add(New System.Net.IPAddress(New Byte() {192, 168, 1, 2}))
        _list.Add(New System.Net.IPAddress(New Byte() {192, 168, 1, 3}))
        Dim result = From n In nics Where n.GetIPProperties.DnsAddresses.ContainsAny(_list)
        Console.WriteLine(result.Count)
        result = From n In nics Where n.GetIPProperties.DnsAddresses.ContainsAll(_list)
        Console.WriteLine(result.Count)
        Console.WriteLine()
    End Sub

    &lt;Extension()&gt; _
    Public Function ContainsAny(Of T)(ByVal collection As ICollection(Of T), ByVal Parameters As ICollection(Of T)) As Boolean
        For Each parameter In Parameters
            If collection.Contains(parameter) Then
                Return True
            End If
        Next
        Return False
    End Function

    &lt;Extension()&gt; _
    Public Function ContainsAll(Of T)(ByVal collection As ICollection(Of T), ByVal Parameters As ICollection(Of T)) As Boolean
        For Each parameter In Parameters
            If Not collection.Contains(parameter) Then
                Return False
            End If
        Next
        Return True
    End Function
End Module
```
And this is the result:

> Local area connection
  
> DNS server : 192.168.1.2
  
> DNS server : 192.168.1.3
  
> Local area connection 2
  
> DNS server : 192.168.1.2
  
> DNS server : 192.168.1.3
  
> Local area connection 3
  
> DNS server : 192.168.1.2
  
> DNS server : 192.168.1.3
  
> 3
  
> 0 

These are not the exact results for my machine but you get the point.

It seems to work 😉