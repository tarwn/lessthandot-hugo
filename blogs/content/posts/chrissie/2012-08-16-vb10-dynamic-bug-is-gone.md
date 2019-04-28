---
title: VB10 dynamic bug is gone in VB11
author: Christiaan Baes (chrissie1)
type: post
date: 2012-08-16T17:43:00+00:00
ID: 1695
excerpt: The bug I reported in VB10 (VS2010) is fixed in VB11 (VS2012).
url: /index.php/desktopdev/mstech/vbnet/vb10-dynamic-bug-is-gone/
views:
  - 1569
categories:
  - VB.NET
tags:
  - dynamic
  - vb.net

---
When I was tinkering with Simple.Data I got an error when I tried to this.

```vbnet
Dim result = db.Persons.Query.Join(db.Address).On(db.Persons.AddressId = db.Address.Id).Select(db.Persons.LastName, db.Persons.FirstName, db.Address.Street, db.Address.HouseNumber)
        ```
You get this error.

> Public member &#8216;Address&#8217; on type &#8216;Database&#8217; not found.

you can fix this by doing this.

```vbnet
Dim result = db.Persons.Query.Join((db.Address)).On(db.Persons.AddressId = db.Address.Id).Select(db.Persons.LastName, db.Persons.FirstName, db.Address.Street, db.Address.HouseNumber)
        ```
But that is just freaky as hell.

And I got that suggestion from the always helpful Lucian wischik.

You can &#8220;easily reproduce this by doing this in a consoleapplication.

```
Imports System.Dynamic

Module Module1
    Sub Main()
        Dim c2 As Object = New C
        Dim db2 As Object = New D
        c2.Join(db2.Address)
    End Sub
End Module

Class C : Inherits DynamicObject
    Public Overrides Function TryInvokeMember(binder As System.Dynamic.InvokeMemberBinder, args() As Object, ByRef result As Object) As Boolean
        If binder.Name = "Join" Then
            result = args(0) & args(0)
            Return True
        End If
        Return False
    End Function
End Class

Class D : Inherits DynamicObject
    Public Overrides Function TryGetMember(binder As System.Dynamic.GetMemberBinder, ByRef result As Object) As Boolean
        If binder.Name = "Address" Then
            result = "addr"
            Return True
        End If
        Return False
    End Function
End Class```
When I contacted Lucian about this he promised this would be fixed in VS2012

> Christiaan, thanks for bringing this issue up. We’ve got the fix in for VS11. (It won’t show up in RC, but should be present when it’s released).

If you have installed VS2012 and try this again.

And yes, it is fixed.

But what is more if you kept VS2010 on the same machine and did a side by side install you will notice that this now also compiles in VS2010. This is because the .Net framework 4.5 is an in place upgrade and this bug is a compiler bug and now VS2010 and VS2012 share the same compiler.

And thanks to Craig Murphy for checking that I wasn&#8217;t going insane.