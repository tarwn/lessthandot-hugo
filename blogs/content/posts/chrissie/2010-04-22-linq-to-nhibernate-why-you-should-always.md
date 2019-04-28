---
title: Linq to nHibernate why you should always check the sql it produces.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-22T05:00:47+00:00
ID: 764
url: /index.php/desktopdev/mstech/linq-to-nhibernate-why-you-should-always/
views:
  - 2603
categories:
  - Microsoft Technologies

---
Today I was doing some LINQ to nHibernate and I came to this statement.

Considering that I used a Person class that looks like this.

```vbnet
Public Class Person
  Private _lastName as String
  Private _id as Integer

...

End Class```
And what I wanted was the lowest Id number for all people that were named like me. Because I am sure I will always have the lowest id.

In SQL that would look something like this.

```tsql
Select min(id) from person where person.Lastname = 'baes'```
The first linq statement I came up with was this.

```vbnet
_session.Linq(of Person).Where(Function(x) x.LastName.Equals("baes")).Min(Function(x) x.Id)```
That gave the right result. But it was loading all of the Person objects and then doing the Min function in memory on those Objects.

So I changed it to this.

```vbnet
(From e in _Session.Linq(Of Person) where e.LastName = "baes" select e.Id).Min```
Which did result in the above mentioned select statement.