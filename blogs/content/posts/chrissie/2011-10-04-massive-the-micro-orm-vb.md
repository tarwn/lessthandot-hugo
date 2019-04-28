---
title: Massive (the micro-ORM), VB.Net and Named argument cannot match a paramarray parameter
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-04T06:52:00+00:00
ID: 1339
excerpt: 'I have been playing with Massive the micro-ORM for a week now and it works with VB.Net, just as it works with C#, of course it works, I hear you say. Well, there is no "of course" here, but I will not rant about that now.'
url: /index.php/desktopdev/mstech/massive-the-micro-orm-vb/
views:
  - 3215
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - massive
  - named arguments.
  - vb.net

---
## Introduction

I have been playing with [Massive][1] the micro-ORM for a week now and it works with VB.Net, just as it works with C#, of course it works, I hear you say. Well, there is no &#8220;of course&#8221; here, but I will not rant about that now.

## The offending function

If you go into the Massive.cs file you will find this function.

```csharp
public virtual IEnumerable&lt;dynamic&gt; All(string where = "", string orderBy = "", int limit = 0, string columns = "*", params object[] args)
        {
            string sql = BuildSelect(where, orderBy, limit);
            return Query(string.Format(sql, columns, TableName), args);
        }
        ```
Do you see the <code class="codespan">params object[] args</code> thing?

You can use this quite well in C# with named arguments like this. (from the Massive website)

```csharp
var tbl = new Products();
var products = tbl.All(where: "CategoryID = @0 AND UnitPrice &gt; @1", orderBy: "ProductName", limit: 20, args: 5,20);```
However if you try the same thing in VB.Net.

```vbnet
Dim tbl = New Products()
Dim products = tbl.All(where:= "CategoryID = @0 AND UnitPrice &gt; @1", orderBy:= "ProductName", limit:= 20, args:= 5,20)```
You will get this nice error.

<span class="MT_red">Named argument cannot match a paramarray parameter</span>

And guess what this is [documented behavior][2].

> You have supplied a named argument (specified with the argument&#8217;s declared name followed by a colon and an equal sign, followed by the argument value); however you cannot pass a parameter array by name. When you call the procedure, you supply an indefinite number of comma-separated arguments for the parameter array, and the compiler cannot associate more than one argument with a single name.
> 
> Error ID: BC30587
  
> To correct this error
> 
> Pass the argument by position, rather than by name.

In other words, **<span class="MT_red">don&#8217;t do that</span>**.

So I have to do this instead.

```
Dim tbl = New Products()
Dim products = tbl.All("CategoryID = @0 AND UnitPrice &gt; @1", "ProductName", limit:= 20, "*", 5,20)```
## Conclusion

Sigh!!

 [1]: http://wekeroad.com/helpy-stuff/and-i-shall-call-it-massive
 [2]: http://msdn.microsoft.com/en-us/library/3he0e00k%28v=vs.90%29.aspx