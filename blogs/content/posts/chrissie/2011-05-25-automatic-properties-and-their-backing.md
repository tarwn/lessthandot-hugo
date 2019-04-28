---
title: 'Automatic properties and their backing fields big difference between C# and VB.Net'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-25T06:18:00+00:00
ID: 1188
excerpt: |
  Introduction
  
  When you work with both C# and VB.Net it is always worth knowing the subtle and sometimes not so subtle differences between the two languages. These are known as the WTF moments. And all though they say both languages should be growing m&hellip;
url: /index.php/desktopdev/mstech/automatic-properties-and-their-backing/
views:
  - 6749
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - automatic properties
  - 'c#'
  - vb.net

---
## Introduction

When you work with both C# and VB.Net it is always worth knowing the subtle and sometimes not so subtle differences between the two languages. These are known as the WTF moments. And all though they say both languages should be growing more toward each other they just aren&#8217;t for the moment. Let&#8217;s take the example of automatic properties.

## Automatic properties

This is a perfectly valid class in VB.Net.

```vbnet
Public Class Properties

		Private _Prop2 As String

        Public Property Prop1 As String

		Public Property Prop2 As String
			Get
				Return _prop2
			End Get
			Set(value As String)
				_prop2 = value
			End Set
		End Property

		Public Sub New()
			_Prop1 = "test"
			_prop2 = "prop2"
			Console.WriteLine(Prop1)
			Console.WriteLine(Prop2)
		End Sub

	End Class```
So I have Prop1 as an automatic property and Prop2 as an old-fashioned one. But in the constructor I can call the backing field of Prop1 fairly easily by just adding an underscore in front of it.

Similar code in C# won&#8217;t compile.

```csharp
public class Properties
        {
            private string _prop2;

            public String Prop1 { get; set; }
            public String Prop2 { get { return _prop2; } set { _prop2 = value; } }

            public Properties()
            {
                _prop1 = "test";
                _prop2 = "prop2";
                Console.WriteLine(Prop1);
                Console.WriteLine(Prop2);
            }
        }```
And as far as I can tell there is no way to get to the backing field like you can in VB.Net. In C# the name of the backingfield is this <code class="codespan">&lt;name&gt;k__BackingField</code>.

## Conclusion

Now why is this important I hear you ask. I should never need to use the backingfield of an autoproperty because there is no extra logic that can make it different from what the property will return. Well, sometimes you need to get that field via reflection and then you will need to know the name. And it&#8217;s just cool to know when someone asks you this in an interview ;-). 

BTW this is the kind of thing the VB and C# teams should make the same in the two languages if they can.