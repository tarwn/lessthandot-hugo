---
title: 'VB.Net: Extending String with extension methods for VB9'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-15T06:16:50+00:00
ID: 84
url: /index.php/desktopdev/mstech/vb-net-extending-string-with-extension-m/
views:
  - 5787
categories:
  - Microsoft Technologies
  - VB.NET

---
This article is based on something [Jeff Atwood][1] did. It reminded me of something I missed an something that could save me some time. I need a simple left and right method on a string variable. 

So I went about and did it ;-).

First I googled around a bit to find out how I should do it in VB.Net (plenty of c# examples can be found). And I found this [blogpost by Chris Rock][2] which helped a lot.

So I started of by creating a consoleapplication.

Next up I create a testclass. Called testExtension (I don&#8217;t want to be over creative, it&#8217;s Monday remember). Like most people I use nUnit to do the dirty work and R# to make it more pleasant.

the first test I wrote looks like this

```vbnet
&lt;Test()&gt; _
        Public Sub Test_Left_To_See_If_It_Returns_The_Correct_Result()
            Dim s As String
            s = "areyousureyouwantotestthis?"
            Assert.AreEqual("this?", s.left(5))
        End Sub```
of course that won&#8217;t even compile because it can&#8217;t find method Left in there.

So let&#8217;s give it what it wants.

```vbnet
Imports System.Runtime.CompilerServices

Module Module1
    Public Sub main()

    End Sub

    &lt;Extension()&gt; _
    Public Function Left(ByVal Input As String, ByVal Length As Integer) As String

    End Function


End Module
```
Now it compiles and now the test goes red (:'()

First thing to do is create some more tests.

```vbnet
''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Left_When_Length_Is_0()
            Dim s As String
            s = "areyousureyouwantotestthis?"
            Assert.AreEqual("", s.Left(0))
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Left_When_String_Is_Empty()
            Dim s As String
            s = ""
            Assert.AreEqual("", s.Left(5))
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Left_When_String_is_empty_and_length_is_0()
            Dim s As String
            s = ""
            Assert.AreEqual("", s.Left(0))
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Left_When_length_is_smaller_then_the_length_of_the_string()
            Dim s As String
            s = "his?"
            Assert.AreEqual("his?", s.Left(7))
        End Sub```
I think I now coverd most things. Except one, but who needs nullreferenceexceptions anyway.

Lets run it and again everything is red.

So now lets get some code in there.

```vbnet
&lt;Extension()&gt; _
    Public Function Left(ByVal Input As String, ByVal Length As Integer) As String
        Dim ReturnString As String = ""
        If (Length = 0 Or Input.Length = 0) Then
            ReturnString = ""
        ElseIf (Input.Length &lt;= Length) Then
            ReturnString = Input
        Else
            ReturnString = Input.Substring(0, Length)
        End If
        Return ReturnString
    End Function
```
Look at the parameters. The first one is will actually be used for the type and for the variable you are using it on. When calling the method you never see this parameter again. The second is the only parameter that will be visible to the user. 

And lets run the test again.

Nearly all passed this time, except the first one. But that was my fault.

> NUnit.Framework.AssertionException: String lengths are both 5. Strings differ at index 0.
    
> Expected: &#8220;this?&#8221;
    
> But was: &#8220;areyo&#8221;
    
> &#8212;&#8212;&#8212;&#8211;^

I confused left with right So thats what I get. I have to change the unittest.

When I change it to this it passes.

```vbnet
''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Left_To_See_If_It_Returns_The_Correct_Result()
            Dim s As String
            s = "areyousureyouwantotestthis?"
            Assert.AreEqual("areyo", s.Left(5))
        End Sub```
So now our string has a left method.

Let&#8217;s add a right method shall we.

Here are the tests.

```vbnet
''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Right_To_See_If_It_Returns_The_Correct_Result()
            Dim s As String
            s = "areyousureyouwantotestthis?"
            Assert.AreEqual("this?", s.Right(5))
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Right_When_Length_Is_0()
            Dim s As String
            s = "areyousureyouwantotestthis?"
            Assert.AreEqual("", s.Right(0))
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Right_When_String_Is_Empty()
            Dim s As String
            s = ""
            Assert.AreEqual("", s.Right(5))
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Right_When_String_is_empty_and_length_is_0()
            Dim s As String
            s = ""
            Assert.AreEqual("", s.Right(0))
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_Right_When_length_is_smaller_then_the_length_of_the_string()
            Dim s As String
            s = "his?"
            Assert.AreEqual("his?", s.Right(7))
        End Sub```
create the empty method and run it. See how they fail.

and then create our logic.

```vbnet
&lt;Extension()&gt; _
    Public Function Right(ByVal Input As String, ByVal Length As Integer) As String
        Dim ReturnString As String = ""
        If (Length = 0 Or Input.Length = 0) Then
            ReturnString = ""
        ElseIf (Input.Length &lt;= Length) Then
            ReturnString = Input
        Else
            ReturnString = Input.Substring(Input.Length)
        End If
        Return ReturnString
    End Function```
Which was wrong again, silly me forgot to substract the length form input.length when doing the substring. Luckily I had the unittests to tell me that.

> NUnit.Framework.AssertionException: Expected string length 5 but was 0. Strings differ at index 0.
    
> Expected: &#8220;this?&#8221;
    
> But was: string.Empty
    
> &#8212;&#8212;&#8212;&#8211;^

So here is the new and all improved version.

```vbnet
&lt;Extension()&gt; _
    Public Function Right(ByVal Input As String, ByVal Length As Integer) As String
        Dim ReturnString As String = ""
        If (Length = 0 Or Input.Length = 0) Then
            ReturnString = ""
        ElseIf (Input.Length &lt;= Length) Then
            ReturnString = Input
        Else
            ReturnString = Input.Substring(Input.Length - Length)
        End If
        Return ReturnString
    End Function```
And that&#8217;s it. Have fun.

Just in case you don&#8217;t believe me.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev//Unittestingextensionmethoodsleftandright.JPG" alt="" title="" width="494" height="348" />
</div>

<font color="Red">Need help with VB.Net? Come and ask a question in our <a href="http://forum.ltd.local/viewforum.php?f=39">VB.Net Forum</a></font>

 [1]: http://www.codinghorror.com/blog/archives/001151.html
 [2]: http://www.rocksthoughts.com/blog/archive/2008/03/11/extension-method-implementation-differences-between-c-and-vb.net.aspx