---
title: Yes, I do make stupid mistakes
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-04T07:56:34+00:00
ID: 41
url: /index.php/desktopdev/mstech/vbnet/yes-i-do-make-stupid-misstakes/
views:
  - 3363
categories:
  - VB.NET

---
Granted this one is due to the intellisense being a bit overactive, but can you spot the mistake?

```vbnet
''' &lt;summary&gt;
''' Select this record for printing
''' &lt;/summary&gt;
''' &lt;value&gt;&lt;/value&gt;
''' &lt;returns&gt;&lt;/returns&gt;
''' &lt;remarks&gt;&lt;/remarks&gt;
&lt;NHibernate.Mapping.Attributes.Property()&gt; _
Public Property Printed() As Boolean Implements Interfaces.ICDCover.Printed
  Get
    Return _printed
  End Get
  Set(ByVal value As Boolean)
    _printed = False
  End Set
End Property```
I can :oops:.