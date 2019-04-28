---
title: Getting the UnderLyingType of a Generic Object in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-27T10:33:33+00:00
ID: 446
url: /index.php/desktopdev/mstech/getting-the-underlyingtype-of-a-generic/
views:
  - 4164
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - .net
  - reflection
  - vb.net

---
I was in need of some reflection to determine the underlying type when I used Nullable. For instance <code class="codespan">Nullable(Of Date)</code>.

First of course I did a Google. And I found [Get underlying type from a nullable type][1] by [Rydal Williams][2].

And there I found the C# version of how to do this. So I translated that and I found something new. Watch carefully.

```vbnet
For Each _PropertyInfo In _model.GetType.GetProperties(Reflection.BindingFlags.Public or Reflection.BindingFlags.Instance)
  If _PropertyInfo.PropertyType.IsGenericType AndAlso _PropertyInfo.PropertyType.GetGenericTypeDefinition().Equals(gettype(Nullable(of ))) Then
    System.Console.WriteLine(Nullable.GetUnderlyingType(_propertyInfo.PropertyType).FullName)
  Else
    System.Console.WriteLine(_PropertyInfo.PropertyType.FullName)
  End If
Next```
Can you see it. <code class="codespan">GetType(Nullable(Of ))</code> no type after the Of, I neve thought you could do that.

You could of course do the same for fields.

```vbnet
For Each _FieldInfo In _model.GetType.GetFields(Reflection.BindingFlags.Public or Reflection.BindingFlags.Instance or Reflection.BindingFlags.NonPublic)
  If _FieldInfo.FieldType.IsGenericType AndAlso _FieldInfo.FieldType.GetGenericTypeDefinition().Equals(gettype(Nullable(of ))) Then
    System.Console.WriteLine(Nullable.GetUnderlyingType(_FieldInfo.FieldType).FullName)
  Else
    System.Console.WriteLine(_FieldInfo.FieldType.FullName)
  End If
Next```
Since this is reflection you won&#8217;t really need this much but from time to time it comes in handy. Untill we get better lambda support in VB10 this will have to do.

One learns something new every day and the you die.

 [1]: http://blog.dotnetclr.com/archive/2008/03/11/get-underlying-type-from-a-nullable-type.aspx
 [2]: http://blog.dotnetclr.com/