---
title: How to make your VB.Net code completely unreadable but still compile.
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-23T17:14:00+00:00
ID: 1327
excerpt: |
  Just use the colon everywhere you can. I twill make this class.
  
  Public Class Person1
  
          Public Sub New(ByVal country As String, ByVal postcode As String, ByVal town As String, ByVal housenumber As String, ByVal street As String, ByVal firstna&hellip;
url: /index.php/desktopdev/mstech/how-to-make-your-vb/
views:
  - 5021
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - colon
  - vb.net

---
Just use the colon everywhere you can. I twill make this class.

```vbnet
Public Class Person1

        Public Sub New(ByVal country As String, ByVal postcode As String, ByVal town As String, ByVal housenumber As String, ByVal street As String, ByVal firstname As String, ByVal name As String)
            _country = country
            _postcode = postcode
            _town = town
            _housenumber = housenumber
            _street = street
            _firstname = firstname
            _name = name
        End Sub

        Private _country As String
        Private _postcode As String
        Private _town As String
        Private _housenumber As String
        Private _street As String
        Private _firstname As String
        Private _name As String

        Public Property Country() As String
            Get
                Return _country
            End Get
            Set (ByVal value As String)
                _country = value
            End Set
        End Property

        Public Property Postcode() As String
            Get
                Return _postcode
            End Get
            Set (ByVal value As String)
                _postcode = value
            End Set
        End Property

        Public Property Town() As String
            Get
                Return _town
            End Get
            Set (ByVal value As String)
                _town = value
            End Set
        End Property

        Public Property Housenumber() As String
            Get
                Return _housenumber
            End Get
            Set (ByVal value As String)
                _housenumber = value
            End Set
        End Property

        Public Property Street() As String
            Get
                Return _street
            End Get
            Set (ByVal value As String)
                _street = value
            End Set
        End Property

        Public Property Firstname() As String
            Get
                Return _firstname
            End Get
            Set (ByVal value As String)
                _firstname = value
            End Set
        End Property

        Public Property Name() As String
            Get
                Return _name
            End Get
            Set (ByVal value As String)
                _name = value
            End Set
        End Property
        
        Public Overloads Function Equals(ByVal other As Person1) As Boolean
            If ReferenceEquals(Nothing, other) Then Return False
            If ReferenceEquals(Me, other) Then Return True
            Return Equals(other._Country, _Country) AndAlso Equals(other._Postcode, _Postcode) AndAlso Equals(other._Town, _Town) AndAlso Equals(other._HouseNumber, _HouseNumber) AndAlso Equals(other._Street, _Street) AndAlso Equals(other._Firstname, _Firstname) AndAlso Equals(other._Name, _Name)
        End Function

        Public Overloads Overrides Function Equals(ByVal obj As Object) As Boolean
            If ReferenceEquals(Nothing, obj) Then Return False
            If ReferenceEquals(Me, obj) Then Return True
            If Not Equals(obj.GetType(), GetType(Person)) Then Return False
            Return Equals(DirectCast(obj, Person))
        End Function

        Public Overrides Function GetHashCode() As Integer
            Dim hashCode As Long = 0
            If _Country IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Country.GetHashCode()) Mod Integer.MaxValue)
            If _Postcode IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Postcode.GetHashCode()) Mod Integer.MaxValue)
            If _Town IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Town.GetHashCode()) Mod Integer.MaxValue)
            If _HouseNumber IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _HouseNumber.GetHashCode()) Mod Integer.MaxValue)
            If _Street IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Street.GetHashCode()) Mod Integer.MaxValue)
            If _Firstname IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Firstname.GetHashCode()) Mod Integer.MaxValue)
            If _Name IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Name.GetHashCode()) Mod Integer.MaxValue)
            Return CInt(hashCode Mod Integer.MaxValue)
        End Function

        Public Overrides Function ToString() As String
            Return String.Format("Country        {0}, Postcode        {1}, Town        {2}, Housenumber        {3}, Street        {4}, Firstname        {5}, Name        {6}", _Country, _Postcode, _Town, _HouseNumber, _Street, _Firstname, _Name)
        End Function
        
    End Class```
into this.

```vbnet
Public Class Person
    Public Sub New(ByVal country As String, ByVal postcode As String, ByVal town As String, ByVal housenumber As String, ByVal street As String, ByVal firstname As String, ByVal name As String)
        _Country = country : _Postcode = postcode : _Town = town : _HouseNumber = housenumber : _Street = street : _Firstname = firstname : _Name = name
    End Sub
    private _country As String:private _postcode As String:private _town As String:private _housenumber As String:private _street As string:Private _firstname As String:Private _name As String:Public Property Name() As String:Get:Return _name:End Get:Set(ByVal value As String):_name = value:End Set:End Property:Public Property Firstname() As String:Get:Return _firstName:End Get:Set(ByVal value As String):_firstname = value:End Set:End Property:Public Property Street() As String:Get:Return _street:End Get:Set(ByVal value As String):_street = value:End Set:End Property:Public Property HouseNumber() As String:Get:Return _housenumber:End Get:Set(ByVal value As String):_housenumber = value:End Set:End Property:Public Property Town() As String:Get:Return _town:End Get:Set(ByVal value As String):_town = value:End Set:End Property:Public Property Postcode() As String:Get:Return _postcode:End Get:Set(ByVal value As String):_postcode = value:End Set:End Property:Public Property Country() As String:Get:Return _country:End Get:Set(ByVal value As String):_country = value:End Set:End Property
    Public Overloads Function Equals(ByVal other As Person) As Boolean
        : If ReferenceEquals(Nothing, other) Then Return False
        : If ReferenceEquals(Me, other) Then Return True
        : Return Equals(other._Country, _Country) AndAlso Equals(other._Postcode, _Postcode) AndAlso Equals(other._Town, _Town) AndAlso Equals(other._HouseNumber, _HouseNumber) AndAlso Equals(other._Street, _Street) AndAlso Equals(other._Firstname, _Firstname) AndAlso Equals(other._Name, _Name)
    End Function
    Public Overloads Overrides Function Equals(ByVal obj As Object) As Boolean
        : If ReferenceEquals(Nothing, obj) Then Return False
        : If ReferenceEquals(Me, obj) Then Return True
        : If Not Equals(obj.GetType(), GetType(Person)) Then Return False
        : Return Equals(DirectCast(obj, Person))
    End Function
    Public Overrides Function GetHashCode() As Integer
        : Dim hashCode As Long = 0 : If _Country IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Country.GetHashCode()) Mod Integer.MaxValue) : If _Postcode IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Postcode.GetHashCode()) Mod Integer.MaxValue) : If _Town IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Town.GetHashCode()) Mod Integer.MaxValue) : If _HouseNumber IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _HouseNumber.GetHashCode()) Mod Integer.MaxValue) : If _Street IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Street.GetHashCode()) Mod Integer.MaxValue) : If _Firstname IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Firstname.GetHashCode()) Mod Integer.MaxValue) : If _Name IsNot Nothing Then hashCode = CInt(((hashCode * 397) Xor _Name.GetHashCode()) Mod Integer.MaxValue)
        : Return CInt(hashCode Mod Integer.MaxValue)
    End Function
    Public Overrides Function ToString() As String
        : Return String.Format("Country: {0}, Postcode: {1}, Town: {2}, Housenumber: {3}, Street: {4}, Firstname: {5}, Name: {6}", _Country, _Postcode, _Town, _HouseNumber, _Street, _Firstname, _Name)
    End Function
End Class
```
Be reminded if you do this in my code I will slowly torture you. But it can be used for fun purposes ;-).

The colon [has been there for a while now][1], might even been in VB6. And God knows why did haven&#8217;t removed this yet.

 [1]: http://msdn.microsoft.com/en-us/library/865x40k4%28v=VS.71%29.aspx