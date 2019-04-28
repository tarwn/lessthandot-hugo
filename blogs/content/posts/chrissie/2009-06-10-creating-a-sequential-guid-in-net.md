---
title: Creating a Sequential Guid in .Net
author: Christiaan Baes (chrissie1)
type: post
date: 2009-06-10T10:50:07+00:00
ID: 464
url: /index.php/desktopdev/mstech/creating-a-sequential-guid-in-net/
views:
  - 20392
categories:
  - Microsoft Technologies
tags:
  - sequentialguid
  - vb.net

---
Today I needed to create a sequential GUid in .Net to test if my Compareto function worked as expected.

The problem is that <code class="codespan">Guid.NewGuid</code> produces a random Guid duh. which isn&#8217;t sequential. Of course in my unittests I want to know what value I&#8217;m giving to the guid.

.Net doesn&#8217;t have a SequentialGuid function as far as I know So went for a little Google. And I found this site.

[Generate Sequential GUIDs for SQL Server 2005 in C#][1]

Which has an interesting concept. But I needed no such difficult things for my unittests.

The following would prove my point just as well.

```vbnet
ObjectThatHasGuid1 = New ObjectThatHasGuid()
ObjectThatHasGuid1.GetType.GetField("_id",Reflection.BindingFlags.NonPublic or Reflection.BindingFlags.Instance).SetValue(ObjectThatHasGuid1,new Guid("00000000-0000-0000-0000-000000000001"))
ObjectThatHasGuid2 = New ObjectThatHasGuid()
ObjectThatHasGuid2.GetType.GetField("_id",Reflection.BindingFlags.NonPublic or Reflection.BindingFlags.Instance).SetValue(ObjectThatHasGuid2,new Guid("00000000-0000-0000-0000-000000000002"))```
Life can be so easy sometimes if you know how. I totaly forgot that GUID was an object and that you can instantiate it.

 [1]: http://developmenttips.blogspot.com/2008/03/generate-sequential-guids-for-sql.html