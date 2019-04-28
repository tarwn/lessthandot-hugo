---
title: Making automated tests for my resharper plugin
author: Christiaan Baes (chrissie1)
type: post
date: 2012-09-29T13:11:00+00:00
ID: 1737
excerpt: Sometime in the past I made a blogpost on how I made a resharper plugin. This is a follow up post on how to test it.
url: /index.php/desktopdev/mstech/making-automated-tests-for-my/
views:
  - 3553
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - plugin
  - resharper
  - vb.net

---
Sometime in the past I made a blogpost on [how I made a resharper plugin][1].

First warning. Running your tests will be slow at best. This is because the sdk creates the whole visual studio solution in memory when you run your tests. 

You can find [all the code on github][2].

The resharper SDK makes it &#8220;easy&#8221; for you to test. They have automated a lot and you just have to do some simple things and all the magic will be done for you.

Alas, documentation is not so brilliant. So most of this I had to found out by myself or ask Matt Ellis.

The contextaction I made has two methods Execute and IsAvailable. And I found out that you have two sort of tests for that. The Execute tests and the Availability tests.

Firt you create a resharper testproject.

Lets start with the Availability tests. 

First of all you start by making a class that inherits from VBContextActionAvailabilityTestBase or CSharpContextActionAvailabilityTestBase. Then you create your testdata.

For this you need to create a folder. test with a folder data in it. Do this in your testproject and make all the files you add to that have a build action of none and copy to output directory to copy always. That way you can have all your files there without them being compiled.

Now we create our first file.

This is Class1.vb

```vbnet
Public Class Cla{off}ss1
    {off}
End Class```
The key thing to see here is the {off} placeholder. The test will look for the {on} and {off} placeholder. It will then check to see if your ContextAction is available in those places. When it finds an off the IsAvailable method should return false. When IsAvailable is true it should find an on in that place.

So the above test should pass. because I don&#8217;t want to have the contextaction when filename and type are the same or in any other place.

Our second testfile called Calss2.vb looks like this.

```vbnet
Public Class Cla{on}ss1
    {off}
End Class```
In this case the filename and typename do not match and thus IsAvailable should return true and show the contextaction.

I added a few more files to test as you can see on github.

And then I need to tell it which files it should test.

```vbnet
Imports ClassNameToFileNameQuickfix
Imports JetBrains.ReSharper.Intentions.Extensibility
Imports JetBrains.ReSharper.Feature.Services.VB.Bulbs
Imports NUnit.Framework
Imports JetBrains.ReSharper.Intentions.VB.Tests.ContextActions

Public Class VBAvailabiltyTests
    Inherits VBContextActionAvailabilityTestBase

    &lt;Test()&gt;
    Public Sub AvailabilityTestClass1vb()
        DoTestFiles("Class1.vb")
    End Sub

    &lt;Test()&gt;
    Public Sub AvailabilityTestClass2vb()
        DoTestFiles("Class2.vb")
    End Sub

    Protected Overrides ReadOnly Property ExtraPath() As String
        Get
            Return "TestsAvailability"
        End Get
    End Property

    Protected Overrides ReadOnly Property RelativeTestDataPath() As String
        Get
            Return "TestsAvailability"
        End Get
    End Property

    Protected Overrides Function CreateContextAction(ByVal dataProvider As IVBContextActionDataProvider) As IContextAction
        Return New ClassNameToFileNameQuickFixActionForVB(dataProvider)
    End Function
End Class```
you do this by simply calling dotestfiles and then giving it the testclassname. YOu will also have to set the extrapath if you created folder in test/data.

And in CreateContextAction you instantiate the contextaction you want to test.

So now on to the executetests.

Here you have to creat two testfiles. One with the code before the action is executed and one with the desired result. This desired result file has the same name as the before file with an extension of .gold.

So I created a Class2.vb file.

```vbnet
Public Class Cla{caret}ss1

End Class```
In this case you use a {caret} placeholder to tell the testcode where to invoke the contextaction. 

And then we have a Class2.vb.gold file.

```vbnet
Public Class Cla{caret}ss2

End Class```
As you can see we want to change the typename from Class1 to Class2.

And here is the code that will check the above.

```vbnet
Imports ClassNameToFileNameQuickfix
Imports JetBrains.ReSharper.Intentions.Extensibility
Imports JetBrains.ReSharper.Feature.Services.VB.Bulbs
Imports NUnit.Framework
Imports JetBrains.ReSharper.Intentions.VB.Tests.ContextActions

Public Class VBExecuteTests
    Inherits VBContextActionExecuteTestBase

    &lt;Test()&gt;
    Public Sub ExecuteTestClass2Vb()
        DoTestFiles("Class2.vb")
    End Sub

    Protected Overrides ReadOnly Property ExtraPath() As String
        Get
            Return "TestsExecute"
        End Get
    End Property

    Protected Overrides ReadOnly Property RelativeTestDataPath() As String
        Get
            Return "TestsExecute"
        End Get
    End Property

    Protected Overrides Function CreateContextAction(ByVal dataProvider As IVBContextActionDataProvider) As IContextAction
        Return New ClassNameToFileNameQuickFixActionForVB(dataProvider)
    End Function
End Class```
The biggest difference with the availability tests is that you now inherit from VBContextActionExecuteTestBase or CSharpContextActionExecuteTestBase.

After creating all these tests I noticed I had more than a few tests that were failing.

This was my code at the time.

```vbnet
Imports JetBrains.ReSharper.Feature.Services.Bulbs
Imports JetBrains.ReSharper.Intentions.Extensibility
Imports JetBrains.ProjectModel
Imports JetBrains.Application.Progress
Imports JetBrains.TextControl
Imports JetBrains.Util
Imports JetBrains.ReSharper.Psi.Tree
Imports JetBrains.ReSharper.Psi.VB.Tree

Namespace ClassNameToFileNameQuickfix
    Public Class ClassNameToFileNameQuickFixActionBase
        Inherits ContextActionBase

        Private _provider As IContextActionDataProvider

        Public Sub New(dataProvider As IContextActionDataProvider)
            _provider = dataProvider
        End Sub

        Public Overrides ReadOnly Property Text As String
            Get
                Return "Rename class to match filename"
            End Get
        End Property

        Protected Overrides Function ExecutePsiTransaction(ByVal solution As ISolution, ByVal progress As IProgressIndicator) As System.Action(Of ITextControl)
            Dim literal = _provider.GetSelectedElement(Of JetBrains.ReSharper.Psi.Tree.ITypeDeclaration)(True, True)
            If (literal IsNot Nothing) Then
                literal.SetName(literal.GetSourceFile().Name.Substring(0, literal.GetSourceFile().Name.Length - 3))
            End If
            Return Nothing
        End Function

        Public Overrides Function IsAvailable(ByVal cache As IUserDataHolder) As Boolean
            Dim literal = _provider.GetSelectedElement(Of JetBrains.ReSharper.Psi.Tree.ITypeDeclaration)(True, True)
            If (literal IsNot Nothing AndAlso Not literal.DeclaredName = literal.GetSourceFile().Name.Substring(0, literal.GetSourceFile().Name.Length - 3)) Then
                Return True
            End If
            Return False
        End Function
    End Class
End NameSpace```
And it was Avaibility test of Class2 that was failing.

With this error message.

> Incorrect state at offset 25.
    
> Supposed to be False:off:, but was True
    
> Public Class Class1
        
> |HERE|
    
> End Class
      
> Expected: False
      
> But was: True

The problem being that the contextaction would show up anywhere in the type. That is not what I was aiming for. I was aiming for typename only.

And 2 hours later I end up with this.

```vbnet
Public Overrides Function IsAvailable(ByVal cache As IUserDataHolder) As Boolean
            Dim selectedvbelement = _provider.SelectedElement.Parent.Parent
            Dim selectedcsharpelement = _provider.SelectedElement.Parent
            Dim literal = _provider.GetSelectedElement(Of JetBrains.ReSharper.Psi.Tree.ITypeDeclaration)(True, True)
            If (literal IsNot Nothing AndAlso Not literal.DeclaredName = literal.GetSourceFile().Name.Substring(0, literal.GetSourceFile().Name.Length - 3)) AndAlso (TypeOf selectedvbelement Is JetBrains.ReSharper.Psi.VB.Tree.IClassDeclaration OrElse TypeOf selectedcsharpelement Is JetBrains.ReSharper.Psi.CSharp.Tree.IClassDeclaration OrElse TypeOf selectedvbelement Is JetBrains.ReSharper.Psi.VB.Tree.IInterfaceDeclaration OrElse TypeOf selectedcsharpelement Is JetBrains.ReSharper.Psi.CSharp.Tree.IInterfaceDeclaration OrElse TypeOf selectedvbelement Is JetBrains.ReSharper.Psi.VB.Tree.IModuleDeclaration) Then
                Return True
            End If
            Return False
        End Function```
And all current tests pass. Look at the difference between the VB.net code and the C# code. Not funny.

Anyway it works. A lot of this is thanks to Matt Ellis.

 [1]: /index.php/All/?p=1830
 [2]: https://github.com/chrissie1/ResharperpluginClassNameToFileNameQuickFix