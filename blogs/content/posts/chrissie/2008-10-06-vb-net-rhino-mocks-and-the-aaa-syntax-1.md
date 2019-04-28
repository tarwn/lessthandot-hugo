---
title: 'VB.Net: Rhino Mocks and the AAA syntax'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-10-06T08:44:43+00:00
ID: 162
url: /index.php/desktopdev/mstech/vb-net-rhino-mocks-and-the-aaa-syntax-1/
views:
  - 8052
categories:
  - Microsoft Technologies
tags:
  - ayende
  - rhino mocks
  - vb.net

---
So, last week [Ayende][1] made a new version of Rhino Mocks available, namely version 3.5.  [He made a post on what is new and shiny][2], but I would like to look at what the new AAA syntax has in store for us VB.Net users. 

And I want to tell you how it used to look and how it looks now.

This is the old version, works with **Rhino mocks 3.4**.

```vbnet
Imports NUnit.Framework
Imports Rhino.Mocks

Namespace AnalysisManagement
    &lt;TestFixture()&gt; _
    &lt;Category("ModelServices")&gt; _
    &lt;Category("ModelServices.AnalysisManagement")&gt; _
    Public Class TestDAOFill

#Region " Private members "
        Private _Mocker As MockRepository
        Private _Factory As DataAccessLayer.AnalysisManagement.Crud.Factory.IDAOFactory
        Private _Fill As ModelServices.AnalysisManagement.Interfaces.IFill
#End Region

#Region " Setup "
        &lt;SetUp()&gt; _
        Public Sub Setup()
            _Mocker = New MockRepository()
            _Factory = _Mocker.DynamicMock(Of DataAccessLayer.AnalysisManagement.Crud.Factory.IDAOFactory)()
            _MspFactory = _Mocker.DynamicMock(Of DataAccessLayer.MSP.Crud.Factory.IDAOFactory)()
            _Fill = New ModelServices.AnalysisManagement.DAOFill(_Factory, _MspFactory)
        End Sub

        &lt;TearDown()&gt; _
        Public Sub TearDown()
            _Mocker.ReplayAll()
            &lt;em&gt;_Mocker.VerifyAll()&lt;/em&gt;
        End Sub
#End Region

#Region " Tests "
&lt;Test()&gt; _
        Public Sub FindAllFiberFluorescenceColorN21s()
            Dim _CrudIFiberFluorescenceColorN21 As DataAccessLayer.AnalysisManagement.Crud.Interfaces.IFiberFluorescenceColorN21 = _Mocker.DynamicMock(Of DataAccessLayer.AnalysisManagement.Crud.Interfaces.IFiberFluorescenceColorN21)()
            &lt;em&gt;Expect.Call&lt;/em&gt;(_Factory.CrudFiberFluorescenceColorN21).IgnoreArguments.Return(_CrudIFiberFluorescenceColorN21)
            &lt;em&gt;Expect.Call&lt;/em&gt;(_CrudIFiberFluorescenceColorN21.FindAll).IgnoreArguments.Return(New List(Of Model.AnalysisManagement.Interfaces.IFiberFluorescenceColorN21))
            &lt;em&gt;_Mocker.ReplayAll()&lt;/em&gt;
            Dim _templist As IList(Of Model.AnalysisManagement.Interfaces.IFiberFluorescenceColorN21) = _Fill.FindAllFiberFluorescenceColorN21s
            Assert.IsNotNull(_templist)
            Assert.IsInstanceOfType(GetType(IList(Of Model.AnalysisManagement.Interfaces.IFiberFluorescenceColorN21)), _templist)
        End Sub
#End Region
End Class
End Namespace
```
Ok, so we notice the use of _mocker and DynamicMock to create our Mock objects and then we set our expectations, then we do a replayall and execute the methods under test. When the test is done the verifyall is executed which looks if our expectations are met.

Now we switch to **Rhino mocks 3.5** and see that we get an error on Expect saying that the signature is not correct. No worry this is because it is choosing the wrong Expect. It is namely trying to use the extension method there. Just add Rhino.Mocks. before the Expect and all is well again. Look how the imports **doesn&#8217;t** do the same thing.

And now on to the new.

```vbnet
Imports NUnit.Framework
Imports Rhino.Mocks

Namespace AnalysisManagement
    &lt;TestFixture()&gt; _
    &lt;Category("ModelServices")&gt; _
    &lt;Category("ModelServices.AnalysisManagement")&gt; _
    Public Class TestDAOFill

#Region " Private members "
        Private _Factory As DataAccessLayer.AnalysisManagement.Crud.Factory.IDAOFactory
        Private _Fill As ModelServices.AnalysisManagement.Interfaces.IFill
#End Region

#Region " Setup "
        &lt;SetUp()&gt; _
        Public Sub Setup()
            _Factory = &lt;em&gt;MockRepository.GenerateMock&lt;/em&gt;(Of DataAccessLayer.AnalysisManagement.Crud.Factory.IDAOFactory)()
            _MspFactory = &lt;em&gt;MockRepository.GenerateMock&lt;/em&gt;(Of DataAccessLayer.MSP.Crud.Factory.IDAOFactory)()
            _Fill = New ModelServices.AnalysisManagement.DAOFill(_Factory, _MspFactory)
        End Sub

        &lt;TearDown()&gt; _
        Public Sub TearDown()
      
        End Sub
#End Region

#Region " Tests "
&lt;Test()&gt; _
        Public Sub FindAllFiberFluorescenceColorN21s()
            Dim _CrudIFiberFluorescenceColorN21 As DataAccessLayer.AnalysisManagement.Crud.Interfaces.IFiberFluorescenceColorN21 = &lt;em&gt;MockRepository.GenerateMock&lt;/em&gt;(Of DataAccessLayer.AnalysisManagement.Crud.Interfaces.IFiberFluorescenceColorN21)()
            _CrudIFiberFluorescenceColorN21.&lt;em&gt;Stub&lt;/em&gt;(Function(e) e.FindAll()).Return(New List(Of Model.AnalysisManagement.Interfaces.IFiberFluorescenceColorN21))
            _Factory.&lt;em&gt;Stub&lt;/em&gt;(Function(e) e.CrudFiberFluorescenceColorN21).Return(_CrudIFiberFluorescenceColorN21)
            Dim _templist As IList(Of Model.AnalysisManagement.Interfaces.IFiberFluorescenceColorN21) = _Fill.FindAllFiberFluorescenceColorN21s
            Assert.IsNotNull(_templist)
            Assert.IsInstanceOfType(GetType(IList(Of Model.AnalysisManagement.Interfaces.IFiberFluorescenceColorN21)), _templist)
        End Sub
#End Region
End Class
End Namespace
```
Look at how the \_mocker.DynamicMock are replaced by MockRepository.GenerateMock and how the \_mocker object has disappeared all together. The Expect.Call&#8217;s have been replaced with Object.Stub methods. And the ReplayAll and VerifyAll are now gone. Overall, a bit simpler and more meaningful.

 [Here is another good article about the use of AAA and Rhino mocks but then in C#.][3]

And it only goes to show that even the smartest and best people don&#8217;t get it right the first time ;D.

Ayende asked to add this to his wiki and so I did http://ayende.com/wiki/Rhino+mocks+3.5+and+VB.Net+the+AAA+syntax.ashx

 [1]: http://ayende.com/Blog/Default.aspx
 [2]: http://ayende.com/Blog/archive/2008/10/05/rhino-mocks-3.5-rtm.aspx
 [3]: http://www.lostechies.com/blogs/jimmy_bogard/archive/2008/10/05/three-simple-rhino-mocks-rules.aspx