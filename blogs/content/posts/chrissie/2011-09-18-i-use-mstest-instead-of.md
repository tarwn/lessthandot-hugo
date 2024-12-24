---
title: I use MSTest instead of Nunit when doing winforms testing.
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-18T06:36:00+00:00
ID: 1319
excerpt: I normally use Nunit for all my unittesting with VB.Net but for winforms testing MS-test is so much easier to use. And I know that in the past there have been attempts to make some frameworks to make this easer but MS-test just makes it trivial to do.
url: /index.php/desktopdev/mstech/i-use-mstest-instead-of/
views:
  - 3082
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - ms test
  - nunit
  - vb.net

---
## Introduction

I normally use Nunit for all my unittesting with VB.Net but for winforms testing MS-test is so much easier to use. And I know that in the past there have been attempts to make some frameworks to make this easer but MS-test just makes it trivial to do.

## MS-test

What we want to test is if our textbox selects all text when we enter it.

Now let&#8217;s try this.

First create a form and then add a textbox to it. Then we add an eventhandler to the Enter event.

```vbnet
Imports System.Threading

Public Class Form1
    
    Private Sub TextBox1_Enter(sender As System.Object, e As System.EventArgs) Handles TextBox1.Enter

    End Sub

End Class
```
So whatever unittest we no create to test if it selectsall text when we enter the textbox should fail.

Creating a unittest for this in MS-test is trivial. Just rightclick the method and hit create unit tests.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mstest/mstest1.png?mtime=1316334059"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mstest/mstest1.png?mtime=1316334059" width="585" height="464" /></a>
</div>

Just select the method you want to test and click enter.

You will then get a new project and this class.

```vbnet
Imports System

Imports Microsoft.VisualStudio.TestTools.UnitTesting

Imports WindowsApplication1



'''&lt;summary&gt;
'''This is a test class for Form1Test and is intended
'''to contain all Form1Test Unit Tests
'''&lt;/summary&gt;
&lt;TestClass()&gt; _
Public Class Form1Test


    Private testContextInstance As TestContext

    '''&lt;summary&gt;
    '''Gets or sets the test context which provides
    '''information about and functionality for the current test run.
    '''&lt;/summary&gt;
    Public Property TestContext() As TestContext
        Get
            Return testContextInstance
        End Get
        Set(value As TestContext)
            testContextInstance = Value
        End Set
    End Property

#Region "Additional test attributes"
    '
    'You can use the following additional attributes as you write your tests:
    '
    'Use ClassInitialize to run code before running the first test in the class
    '&lt;ClassInitialize()&gt;  _
    'Public Shared Sub MyClassInitialize(ByVal testContext As TestContext)
    'End Sub
    '
    'Use ClassCleanup to run code after all tests in a class have run
    '&lt;ClassCleanup()&gt;  _
    'Public Shared Sub MyClassCleanup()
    'End Sub
    '
    'Use TestInitialize to run code before running each test
    '&lt;TestInitialize()&gt;  _
    'Public Sub MyTestInitialize()
    'End Sub
    '
    'Use TestCleanup to run code after each test has run
    '&lt;TestCleanup()&gt;  _
    'Public Sub MyTestCleanup()
    'End Sub
    '
#End Region


    '''&lt;summary&gt;
    '''A test for TextBox1_Enter
    '''&lt;/summary&gt;
    &lt;TestMethod(), _
     DeploymentItem("WindowsApplication1.exe")&gt; _
    Public Sub TextBox1_EnterTest()
        Dim target As Form1_Accessor = New Form1_Accessor() ' TODO: Initialize to an appropriate value
        Dim sender As Object = Nothing ' TODO: Initialize to an appropriate value
        Dim e As EventArgs = Nothing ' TODO: Initialize to an appropriate value
        target.TextBox1_Enter(sender, e)
        Assert.Inconclusive("A method that does not return a value cannot be verified.")
    End Sub

End Class
```
You can delete most of that because most of it is rubbish. All you need is this.

```vbnet
Imports System
Imports Microsoft.VisualStudio.TestTools.UnitTesting
Imports WindowsApplication1

'''&lt;summary&gt;
'''This is a test class for Form1Test and is intended
'''to contain all Form1Test Unit Tests
'''&lt;/summary&gt;
&lt;TestClass()&gt; _
Public Class Form1Test

    '''&lt;summary&gt;
    '''A test for TextBox1_Enter
    '''&lt;/summary&gt;
    &lt;TestMethod(), DeploymentItem("WindowsApplication1.exe")&gt; _
    Public Sub Test_If_Enter_Of_TextBox1_Does_a_selectAll_On_its_Text()
        Dim target As Form1_Accessor = New Form1_Accessor()
        Dim sender As Object = target.TextBox1
        Dim e As EventArgs = Nothing

        target.TextBox1.Text = "test"

        target.TextBox1_Enter(sender, e)

        Assert.AreEqual("test", target.TextBox1.SelectedText)
    End Sub

End Class
```
If you now run this test, you will get this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mstest/mstest2.png?mtime=1316334400"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mstest/mstest2.png?mtime=1316334400" width="566" height="148" /></a>
</div>

Now we change our code to this.

```
Imports System.Threading

Public Class Form1
    
    Private Sub TextBox1_Enter(sender As System.Object, e As System.EventArgs) Handles TextBox1.Enter
        CType(sender, TextBox).SelectAll()
    End Sub

End Class
```
And we run the test again to see the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mstest/mstest3.png?mtime=1316334574"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mstest/mstest3.png?mtime=1316334574" width="539" height="144" /></a>
</div>

## Conclusion

As you may well know a winform is normally hard to test because you will have to do a show to have it initialize properly. MS-test does this for you. MS-test also lets you invoke private methods as we have seen above, which in this case is not so bad because it prevents us from showing the form to just run our tests. And of course you can abuse just about anything with this kind of testing so use wisely. For me this is a nice way to test your view. And we all know how hard it is to test a view.