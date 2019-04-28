---
title: 'VB.Net: Rhino mocks 3.5 and Lambda expressions and AssertWasCalled not  always working'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-10-07T10:59:46+00:00
ID: 167
url: /index.php/desktopdev/mstech/vb-net-rhino-mocks-3-5-and-lambda-expres/
views:
  - 6148
categories:
  - Microsoft Technologies
tags:
  - problem
  - rhino mocks
  - vb.net

---
There is no bug in Rhino mocks if that&#8217;s what you&#8217;re thinking. Rhino mocks 3.5 just uses things that VB.Net 9.0 can&#8217;t use and that can be frustrating. I wonder what possessed them to do it like that? 

So let me tell the story.

I got a service and a repository, the service calls the repo.

Repo interface:

```vbnet
Namespace Repository
    Public Interface IRepository(Of T)
        Function FindAll() As IList(Of T)
        Sub Update(ByVal ObjectToUpdate As T)
    End Interface
End Namespace```
The repo implementation:

```vbnet
Imports System.Data.SqlClient

Namespace Repository
    Public Class ProductRepository
        Implements IRepository(Of Model.Product)

        ''' &lt;summary&gt;
        ''' Here is where you would go to the database
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;A list of Products&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Function FindAll() As System.Collections.Generic.IList(Of Model.Product) Implements IRepository(Of Model.Product).FindAll
            'Do something
            Return Nothing
        End Function

        Public Sub Update(ByVal ObjectToUpdate As Model.Product) Implements IRepository(Of Model.Product).Update
            'Do something
        End Sub
    End Class
End Namespace```
The service interface:

```vbnet
Namespace Service
    Public Interface IService
        Function FindAllProducts() As IList(Of Model.Product)
        Sub Update(ByVal Product As Model.Product)
    End Interface
End Namespace```
The service implementation:

```vbnet
Namespace Service
    Public Class Service
        Implements IService

        Private _Repository As Repository.IRepository(Of Model.Product)

        Public Sub New(ByVal Repository As Repository.IRepository(Of Model.Product))
            _Repository = Repository
        End Sub

        Public Function FindAllProducts() As IList(Of Model.Product) Implements IService.FindAllProducts
            Return _Repository.FindAll
        End Function

        Public Sub Update(ByVal Product As Model.Product) Implements IService.Update
            _Repository.Update(Product)
        End Sub
    End Class
End Namespace```
And now we want to test it.

First, we test the service&#8217;s findall method. We mock out the repo and see if the repo was called.

```vbnet
&lt;Test()&gt; _
        Public Sub Test_If_Service_Returns_A_List_Of_Products()
            Dim _Repository As Repository.IRepository(Of Model.Product) = MockRepository.GenerateMock(Of Repository.IRepository(Of Model.Product))()
            Dim _Service As Service.IService = New Service.Service(_Repository)
            _Repository.Stub(Function(e) e.FindAll).IgnoreArguments.Return(New List(Of Model.Product))
            Assert.IsNotNull(_Service.FindAllProducts)
            _Repository.AssertWasCalled(Function(e) e.FindAll)
        End Sub```
This works just fine.

Now I want to see if the update method is also used as it should be, so I write this:

```vbnet
&lt;Test()&gt; _
        Public Sub Test_If_Service_Updates_A_Product()
            Dim _Repository As Repository.IRepository(Of Model.Product) = MockRepository.GenerateMock(Of Repository.IRepository(Of Model.Product))()
            Dim _Service As Service.IService = New Service.Service(_Repository)
            _Service.Update(Nothing)
            _Repository.AssertWasCalled(Function(e) e.Update(Nothing))
        End Sub```
But that gives me an error &#8220;Expression does not produce a value.&#8221;. Mmmm, you can find an explanation for that [here][1]. And the solutions is there as well. The solution is, however, a bit clunky, as he says.

So in the end you could do this:

```vbnet
&lt;Test()&gt; _
        Public Sub Test_If_Service_Updates_A_Product()
            Dim _Repository As Repository.IRepository(Of Model.Product) = MockRepository.GenerateMock(Of Repository.IRepository(Of Model.Product))()
            Dim _Service As Service.IService = New Service.Service(_Repository)
            _Service.Update(Nothing)
            _Repository.AssertWasCalled(Function(e) wrap(e))
        End Sub

        Function wrap(ByRef repo As Repository.IRepository(Of Model.Product)) As Object
            repo.Update(Nothing)
            Return Nothing
        End Function
```
Which solve the problem, more or less.

 [1]: http://www.developer.com/net/vb/article.php/3729301