---
title: linq to nhibernate and subqueries.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-13T15:50:51+00:00
ID: 757
url: /index.php/desktopdev/mstech/linq-to-nhibernate-and-subqueries/
views:
  - 5260
categories:
  - Microsoft Technologies
tags:
  - linq
  - nhibernate
  - subquery
  - vb.net

---
Ok, time to try the linq to nhibernate subquery possibilities. 

Let&#8217;s say I have a object called TexCase that has a collection of Tags Of Type Tag. And the Class Tag has a property called Tag of type string.

So I have this HQL query.

```vbnet
_Session = SessionFactory.OpenSession
                _Query = _Session.CreateQuery("select i from TexCase as i join i.Tags t where t.Tag like :Tag")
                _Query.SetString("Tag", ToFind)
                _ReturnList = _Query.List(Of TexCase)()
                ```
Where ToFind is a String (obviously). 

In this case the string can contain wildcards. Not sure if that is a good idea, but anyway. 

So, 

  * %s
  * s%
  * %s%

should all work.

And [NHProf][1] also confirms it.

```tsql
and x1_.Tag like '%s'```
```tsql
and x1_.Tag like 's%'```
```tsql
and x1_.Tag like '%s%'```
The linq query looks something like this.

```vbnet
_Session = SessionFactory.OpenSession
Dim _query = From e In _Session.Linq(Of TexCase)() Where e.Tags.Any(Function(x) x.Tag.Contains(ToFind))
_ReturnList = _Query.ToList```
But that&#8217;s not completely true since the above will give us the following sql.

```tsql
and x1_.Tag like '%s%'```
```tsql
and x1_.Tag like '%s%'```
```tsql
and x1_.Tag like '%s%'```
Yes, the same for all three tests. 

To solve that I needed to do.

```vbnet
_Session = SessionFactory.OpenSession
                Dim _query As IEnumerable(Of TexCase)
                If ToFind.StartsWith("%") AndAlso ToFind.EndsWith("%") Then
                    _query = From e In _Session.Linq(Of TexCase)() Where e.Tags.Any(Function(x) x.Tag.Contains(ToFind.Substring(1, ToFind.Length() - 2)))
                ElseIf ToFind.StartsWith("%") Then
                    _query = From e In _Session.Linq(Of TexCase)() Where e.Tags.Any(Function(x) x.Tag.StartsWith(ToFind.Substring(1)))
                ElseIf ToFind.EndsWith("%") Then
                    _query = From e In _Session.Linq(Of TexCase)() Where e.Tags.Any(Function(x) x.Tag.EndsWith(ToFind.Substring(0, ToFind.Length() - 1)))
                Else
                    _query = From e In _Session.Linq(Of TexCase)() Where e.Tags.Any(Function(x) x.Tag.Equals(ToFind))
                End If
                _ReturnList = _query.ToList```
Eeck. Not pretty. But I think I can make this work better over time.

 [1]: http://nhprof.com/