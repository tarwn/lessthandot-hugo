---
title: VB.net checking the performance of Linq and Max
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-02T07:33:11+00:00
ID: 406
url: /index.php/desktopdev/mstech/vb-net-checking-the-performance-of-linq/
views:
  - 3666
categories:
  - Microsoft Technologies
tags:
  - linq
  - vb.net

---
Last week I was asked to create a normalisation routine for the Msp spectra that my application uses. No worry if you don&#8217;t know what Msp is. The thing is that it needed to find the maximum value in a collection of values and then do a calculation with that number over the other values in that collection.

First the value object.

```vbnet
Namespace Model
    Public Class MspCoordinate
        Private _A As Decimal
        Private _WavelengthInNm As Decimal

        Public Sub New()
            Me.New(0, 0)
        End Sub

        Public Sub New(ByVal A As Decimal, ByVal WavelengthinNm As Decimal)
            Me.A = A
            Me.WavelengthInNm = WavelengthinNm
        End Sub

        Public Property A() As Decimal
            Get
                Return _A
            End Get
            Set(ByVal value As Decimal)
                _A = value
            End Set
        End Property

        Public Property WavelengthInNm() As Decimal
            Get
                Return _WavelengthInNm
            End Get
            Set(ByVal value As Decimal)
                _WavelengthInNm = value
            End Set
        End Property

        Public Overrides Function ToString() as String
            Return string.Format ("_A: {0}, _WavelengthInNm: {1}", _A, _WavelengthInNm)
        End Function

        Public Overloads Function Equals(ByVal obj As MspCoordinate) As Boolean
            If ReferenceEquals(Nothing, obj) Then Return False
            If ReferenceEquals(Me, obj) Then Return True
            Return obj._A = _A

        End Function

        Public Overloads Overrides Function Equals(ByVal obj As Object) As Boolean
            If ReferenceEquals(Nothing, obj) Then Return False
            If ReferenceEquals(Me, obj) Then Return True
            If Not Equals(obj.GetType(), GetType(MspCoordinate)) Then Return False
            Return Equals(DirectCast(obj, MspCoordinate))

        End Function

        Public Overrides Function GetHashCode() As Integer
            Return _A.GetHashCode()
        End Function
    End Class

End Namespace```
And then some unittests to see if what I get from my mspobject is correct.

```vbnet
Imports NUnit.Framework

Namespace Tests
    &lt;TestFixture()&gt; _
    Public Class TestMspLinq
        Dim _msp As Model.MspLinq
        Dim _mspCoordinates As IList(Of Model.MspCoordinate)

        &lt;SetUp()&gt; _
        Public Sub Setup()
            _mspCoordinates = New List(Of Model.MspCoordinate)
            _mspCoordinates.Add(New Model.MspCoordinate(0.2, 380))
            _mspCoordinates.Add(New Model.MspCoordinate(0.21, 381))
            _mspCoordinates.Add(New Model.MspCoordinate(0.22, 382))
            _mspCoordinates.Add(New Model.MspCoordinate(0.23, 383))
            _mspCoordinates.Add(New Model.MspCoordinate(0.25, 384))
            _mspCoordinates.Add(New Model.MspCoordinate(0.22, 385))
            _mspCoordinates.Add(New Model.MspCoordinate(0.23, 386))
            _mspCoordinates.Add(New Model.MspCoordinate(0.24, 387))
            _mspCoordinates.Add(New Model.MspCoordinate(0.25, 388))
            _mspCoordinates.Add(New Model.MspCoordinate(0.2, 389))
            _mspCoordinates.Add(New Model.MspCoordinate(0.2, 390))
            _msp = New Model.MspLinq(_mspCoordinates)
        End Sub

        &lt;Test()&gt; _
        Public Sub MspCoordinatesNormalised_returns_same_number_of_results_as_MspCoordinates()
            Assert.AreEqual(_msp.MspCoordinates.Count, _msp.MspCoordinatesNormalized.Count)
        End Sub

        &lt;Test()&gt; _
        Public Sub MspCoordinatesNormalized_highest_A_should_be_1()
            Assert.AreEqual(1, _msp.MspCoordinatesNormalized(4).A)
        End Sub

        &lt;Test()&gt; _
        Public Sub MspCoordinatesNormalized_lowest_A_should_be_0_8()
            Assert.AreEqual(0.8, _msp.MspCoordinatesNormalized(0).A)
        End Sub
    End Class
End Namespace```
There are more unittests I should have written but the blogpost is getting long enough as it is.

I started of with a baseclass and let the implementation details of the normalization routine over to the subclasses.

BASECLASS

```vbnet
Imports maxcollection.Model

Public MustInherit class MspBase
    Protected _MspCoordinates As IList(Of MspCoordinate)
    Protected _MspCoordinatesNormalised As IList(Of MspCoordinate)

    Public Sub New(ByVal MspCoordinates As IList(Of MspCoordinate))
        Me._MspCoordinates = MspCoordinates
    End Sub

    Public ReadOnly Property MspCoordinates() As IList(Of MspCoordinate)
        Get
            Return _MspCoordinates
        End Get
    End Property

    Public ReadOnly Property MspCoordinatesNormalized() As IList(Of MspCoordinate)
        Get
            If _MspCoordinatesNormalised Is Nothing Then
                CreateNormalizedMspCoordinates()
            End If
            Return _MspCoordinatesNormalised
        End Get
    End Property

    MustOverride Protected Sub CreateNormalizedMspCoordinates()

    Protected Sub CalculateNormalizedCoordinates(ByVal _Max As Decimal)
        _MspCoordinatesNormalised = New List(Of MspCoordinate)
        Dim _MspCoordinate As MspCoordinate
        For Each _coordinate As MspCoordinate In _MspCoordinates
            _MspCoordinate = New MspCoordinate
            _MspCoordinate.A = _coordinate.A / _Max
            _MspCoordinate.WavelengthInNm = _coordinate.WavelengthInNm
            _MspCoordinatesNormalised.Add(_MspCoordinate)
        Next
    End Sub

    Public Overloads Function Equals(ByVal obj As MspLinq) As Boolean
        If ReferenceEquals(Nothing, obj) Then Return False
        If ReferenceEquals(Me, obj) Then Return True
        Return Equals(obj._MspCoordinates, _MspCoordinates)

    End Function

    Public Overloads Overrides Function Equals(ByVal obj As Object) As Boolean
        If ReferenceEquals(Nothing, obj) Then Return False
        If ReferenceEquals(Me, obj) Then Return True
        If Not Equals(obj.GetType(), GetType(MspLinq)) Then Return False
        Return Equals(DirectCast(obj, MspLinq))

    End Function

    Public Overrides Function GetHashCode() As Integer
        If _MspCoordinates IsNot Nothing Then Return _MspCoordinates.GetHashCode()
        Return 0

    End Function

    Public Overrides Function ToString() As String
        Return String.Format("_MspCoordinates: {0}", _MspCoordinates)
    End Function
end class```
SUBCLASS LINQ

```vbnet
Namespace Model
    Public Class MspLinq
        Inherits MspBase

        Public Sub New(ByVal MspCoordinates As IList(Of MspCoordinate))
            MyBase.New(MspCoordinates)
        End Sub

        Protected Overrides Sub CreateNormalizedMspCoordinates()
            Dim _max = (From e In _MspCoordinates Select e.A).Max
            CalculateNormalizedCoordinates(_max)
        End Sub
    End Class
End Namespace```
SUBCLASS FOR

```vbnet
Namespace Model
    Public Class MspFor
        Inherits MspBase

        Public Sub New(ByVal MspCoordinates As IList(Of MspCoordinate))
            MyBase.New(MspCoordinates)
        End Sub

        Protected Overrides Sub CreateNormalizedMspCoordinates()
            Dim _max As Decimal = -100
            For i As Integer = 0 To MspCoordinates.Count - 1
                If _max &lt; MspCoordinates(i).A Then
                    _max = MspCoordinates(i).A
                End If
            Next
            CalculateNormalizedCoordinates(_max)
        End Sub
    End Class
End Namespace```
SUBCLASS FOREACH

```vbnet
Namespace Model
    Public Class MspForEach
        Inherits MspBase

        Public Sub New(ByVal MspCoordinates As IList(Of MspCoordinate))
            MyBase.New(MspCoordinates)
        End Sub

        Protected Overrides Sub CreateNormalizedMspCoordinates()
            Dim _max As Decimal = -100
            For Each _coordinate As MspCoordinate In MspCoordinates
                If _max &lt; _coordinate.A Then
                    _max = _coordinate.A
                End If
            Next
            CalculateNormalizedCoordinates(_max)
        End Sub
    End Class
End Namespace```
I want this to be as fast as possible since I could potentialy be doing this for thousands of Msp&#8217;s at a time. And yes all of them can be shown on the screen.

AS you might have noticed I lazy loaded the normalized collection because I don&#8217;t want the calculations to be done everytime the object is created.

And this a testfixture I created for testing the perfomance of this.

```vbnet
Imports NUnit.Framework

Namespace Tests
    &lt;TestFixture()&gt; _
    Public Class PerformanceTestNormalizingMsp

        Dim _mspLinq As Model.MspLinq
        Dim _mspFor As Model.MspFor
        Dim _mspForEach As Model.MspForEach
        Dim _mspCoordinates As IList(Of Model.MspCoordinate)

        &lt;SetUp()&gt; _
        Public Sub Setup()

        End Sub

        Private Sub SetupCoordinates(ByVal number As Integer)
            _mspCoordinates = New List(Of Model.MspCoordinate)
            Dim _mspcoordinate As Model.MspCoordinate
            Dim _Random As New Random
            For i As Integer = 1 To number
                _mspcoordinate = New Model.MspCoordinate
                _mspcoordinate.A = _Random.NextDouble()
                _mspcoordinate.WavelengthInNm = number
                _mspCoordinates.Add(_mspcoordinate)
            Next
        End Sub

        &lt;Test()&gt; _
        Public Sub PerfomanceOfLinq()
            Dim stopwatch As New Stopwatch
            Dim number As Integer = 100000
            SetupCoordinates(number)
            stopwatch.Start()
            _mspLinq = New Model.MspLinq(_mspCoordinates)
            Assert.AreEqual(number, _mspLinq.MspCoordinatesNormalized.Count)
            stopwatch.Stop()
            Debug.WriteLine("Linq with " & number & " coordinates took " & stopwatch.ElapsedMilliseconds)
        End Sub

        &lt;Test()&gt; _
        Public Sub PerfomanceOfFor()
            Dim stopwatch As New Stopwatch
            Dim number As Integer = 100000
            SetupCoordinates(number)
            stopwatch.Start()
            _mspFor = New Model.MspFor(_mspCoordinates)
            Assert.AreEqual(number, _mspFor.MspCoordinatesNormalized.Count)
            stopwatch.Stop()
            Debug.WriteLine("For with " & number & " coordinates took " & stopwatch.ElapsedMilliseconds)
        End Sub

        &lt;Test()&gt; _
        Public Sub PerfomanceOfForEach()
            Dim stopwatch As New Stopwatch
            Dim number As Integer = 100000
            SetupCoordinates(number)
            stopwatch.Start()
            _mspForEach = New Model.MspForEach(_mspCoordinates)
            Assert.AreEqual(number, _mspForEach.MspCoordinatesNormalized.Count)
            stopwatch.Stop()
            Debug.WriteLine("ForEach with " & number & " coordinates took " & stopwatch.ElapsedMilliseconds)
        End Sub

    End Class
End Namespace```
And these are the results when I ran it with 10000 coordinates in the collection.

<span class="MT_blue">first run</p> 

<p>
  Linq with 10000 coordinates took 40<br /> For with 10000 coordinates took 12<br /> ForEach with 10000 coordinates took 11
</p>

<p>
  second run
</p>

<p>
  Linq with 10000 coordinates took 42<br /> For with 10000 coordinates took 12<br /> ForEach with 10000 coordinates took 11
</p>

<p>
  third run
</p>

<p>
  Linq with 10000 coordinates took 40<br /> For with 10000 coordinates took 11<br /> ForEach with 10000 coordinates took 11</span>
</p>

<p>
  As you can see pretty consistent numbers.
</p>

<p>
  Linq being the slowest by a factor of 4.
</p>

<p>
  Now lets see what happens with 100000 coordinates
</p>

<p>
  <span class="MT_blue">first run</p> 
  
  <p>
    Linq with 100000 coordinates took 152<br /> For with 100000 coordinates took 169<br /> ForEach with 100000 coordinates took 123
  </p>
  
  <p>
    second run
  </p>
  
  <p>
    Linq with 100000 coordinates took 148<br /> For with 100000 coordinates took 163<br /> ForEach with 100000 coordinates took 128
  </p>
  
  <p>
    third run
  </p>
  
  <p>
    Linq with 100000 coordinates took 154<br /> For with 100000 coordinates took 159<br /> ForEach with 100000 coordinates took 126</span>
  </p>
  
  <p>
    Mmm linq has caugth up with the other two. and is even faster than 4.
  </p>
  
  <p>
    When I add another 0 I get these results
  </p>
  
  <p>
    <span class="MT_blue">Linq with 1000000 coordinates took 1464<br /> For with 1000000 coordinates took 1392<br /> ForEach with 1000000 coordinates took 1419</span>
  </p>
  
  <p>
    <span class="MT_red">The differences being small. So what can I conclude of all this.</p> 
    
    <p>
      Not much really. For many elements in the collection linq is just as fast as the others but it is more readable and shorter to write. When it doesn&#8217;t really matter the difference is big but when it does matter the difference is small so we can pick any one of them.</span>
    </p>