---
title: Things I can’t seem to do with Linq to NHibernate
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-19T05:22:06+00:00
ID: 760
url: /index.php/desktopdev/mstech/things-i-can-t-seem-to-do-with-linq-to-n/
views:
  - 2335
categories:
  - Microsoft Technologies
tags:
  - linq
  - nhibernate
  - vb.net

---
I&#8217;m using NHibernate version 2.1.2 GA and the 2.1.2 Linq version that goes with it.

And I like to state that I&#8217;m not complaining, but perhaps I can save some people from losing too much time while trying this. You still have plenty of other options if Linq does not work in your situation. 

So let&#8217;s say I have this Person Object

```vbnet
Public Class Person
  Private _name as String
  Private _addresses as Ilist(Of Address)

...

End Class```
And as we see in the above, also a Class Address

```vbnet
Public Class Address
  Private _straat as String

...

End Class```
I won&#8217;t bore you with the mapping file because they are straightforward.

The thing I want is to have all the address for persons with a name beginning with A (I know it&#8217;s stupid but that&#8217;s not the point)

So I have this HQL that works nicely.

```vbnet
_Query = _Session.CreateQuery("SELECT elements(p.Addresses) FROM Person p  WHERE p.Name LIKE 'A%'")
_ReturnObject = _Query.List(Of Address)()```
This should return me a nice list af Addresses where a Person has a name starting with A (so now the callcenter can start calling those persons ;-))

I cannot make a one statement Linq for this.

The closest I got is this:

```vbnet
Dim _Query = From e In _Session.Linq(Of Person)() Where e.Name.StartsWith("A") Select e
For Each person In _Query
  For Each address In person.Addresses
    _ReturnObject.Add(address)
  Next
Next```
The next thing I found not to be working is Nullables

For example, if you have this Class

```vbnet
Public Class Person
  Private _birthDate as Nullable(Of Date)

...
End Class
```
Considering that BirthDate could be empty (I know, I know but work with me here).

The following HQL works.

```vbnet
_Query = _Session.CreateQuery("from Person p where p.BirthDate&gt;=:FromDate AND p.BirthDate &lt;:ToDate")
_Query.SetDateTime("FromDate", New Date(Year, 1, 1))
_Query.SetDateTime("ToDate", New Date(Year + 1, 1, 1))
_ReturnList = _Query.List(Of Person)()```
The following Linq Statement will return null/nothing.

```vbnet
Dim _Query = From e In _Session.Linq(Of Person)() Where e.BirtDate&gt;=fromdate AndAlso e.BirthDate&lt;ToDate Select e
```
I tried a whole lot of different things but I just can&#8217;t get it to work. So I&#8217;m sticking to HQL or Criteria for that one at the moment.