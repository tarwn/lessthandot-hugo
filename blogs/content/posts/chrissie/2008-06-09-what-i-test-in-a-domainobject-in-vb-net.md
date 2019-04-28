---
title: What I test in a DomainObject in VB.NET using nunit
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-09T11:08:50+00:00
ID: 48
url: /index.php/desktopdev/mstech/what-i-test-in-a-domainobject-in-vb-net/
views:
  - 1389
categories:
  - Microsoft Technologies
  - VB.NET

---
What a long title :D. 

I use [nUnit][1] (funny how they use php for their website) for my testing together with [Resharper][2] they make a good team.

So lets first look at one of my DomainObjects/Entities.

```
Namespace AddressManagement
    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    &lt;NHibernate.Mapping.Attributes.JoinedSubclass(0, Table:="tbl_Country", extendstype:=GetType(Common.Translated), lazy:=False)&gt; _
    Public Class Country
        Inherits Common.Translated
        Implements Interfaces.ICountry

#Region " Private members "
        ''' &lt;summary&gt;
        ''' A local variable called _name of type String
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;Has Property Name&lt;/remarks&gt;
        Private _name As String
#End Region

#Region " Constructors "
        ''' &lt;summary&gt;
        ''' Default empty constructor
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New()
            Me.New("", New Common.Translation(), "")
        End Sub

        ''' &lt;summary&gt;
        ''' Constructor to fill the name. 
        ''' &lt;/summary&gt;
        ''' &lt;param name="Name"&gt;Datatype = String&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New(ByVal Name As String)
            Me.New(Name, New Common.Translation(), "")
        End Sub

        ''' &lt;summary&gt;
        ''' Constructor with all the fields
        ''' &lt;/summary&gt;
        ''' &lt;param name="Name"&gt;DataType = String&lt;/param&gt;
        ''' &lt;param name="description"&gt;&lt;/param&gt;
        ''' &lt;param name="Remarks"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New(ByVal Name As String, ByVal description As Common.Translation, ByVal Remarks As String)
            Me.Name = Name
            Me.Description = description
            Me.Remarks = Remarks
            ClearDirtyStatus()
        End Sub
#End Region

#Region " Public properties "
        ''' &lt;summary&gt;
        ''' This is the Name property.
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;String&lt;/value&gt;
        ''' &lt;returns&gt;String&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;NHibernate.Mapping.Attributes.Key(1, column:="Id")&gt; _
        &lt;NHibernate.Mapping.Attributes.Property(2, length:=50)&gt; _
        Public Overridable Property Name() As String Implements Interfaces.ICountry.Name, Common.Interfaces.ITranslatedSubClass.Key
            Get
                Return _name
            End Get
            Set(ByVal Value As String)
                _name = Value
                SetDirty()
            End Set
        End Property
#End Region

#Region " Overrides ToString "
        ''' &lt;summary&gt;
        ''' Overridden ToString method for this class
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;String&lt;/returns&gt;
        ''' &lt;remarks&gt;The String value of this class represented by the ToStrings of all it's private members&lt;/remarks&gt;
        Public Overrides Function ToString() As String
            Dim ReturnString As New System.Text.StringBuilder()
            If _name IsNot Nothing Then
                ReturnString.Append("[Name: " & _name.ToString() & "]")
            End If
            Return ReturnString.ToString()
        End Function
#End Region

#Region " Overrides Equals "
        ''' &lt;summary&gt;
        ''' Overridden Equals method for this class
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;Boolean&lt;/returns&gt;
        ''' &lt;remarks&gt;The Boolean value indictaing if this is equal to the other class&lt;/remarks&gt;
        Public Overrides Function Equals(ByVal obj As Object) As Boolean
            If obj IsNot Nothing Then
                If obj.GetType Is Me.GetType Then
                    Return Me._name.Equals(CType(obj, Country)._name)
                Else
                    Return False
                End If
            Else
                Return False
            End If
        End Function
#End Region

#Region " Overrides CompareTo "
        ''' &lt;summary&gt;
        ''' Overridden CompareTo method for this class
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;Integer&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Overrides Function CompareTo(ByVal obj As Object) As Integer
            If obj IsNot Nothing Then
                If obj.GetType Is Me.GetType Then
                    Return Me._name.CompareTo(CType(obj, Country)._name)
                Else
                    Return -1
                End If
            Else
                Return -1
            End If
        End Function
#End Region

    End Class
End Namespace
```
There is quit a lot of code in this simple Entity. First of all it inherits from Common.Translated (which I won&#8217;t show. But that in turn inherited from DomainEntity. [Look at my post from a couple of days ago to see what that does.][3]

Then I override (always) tostring, compareto and equals. Then I have 3 constructors and one property. So now we need to test all this.

```
Imports NUnit.Framework

Namespace AddressManagement.Test
    ''' &lt;summary&gt;
    ''' Test Class to test the Country Class
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;Tests all the methods of this class&lt;/remarks&gt;
    &lt;TestFixture(Description:="Test Country")&gt; _
    &lt;Category("Model")&gt; _
    &lt;Category("Model.AddressManagement")&gt; _
    Public Class TestCountry
        Implements Util.Interfaces.ITest

#Region " Private members "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Country1 As Model.AddressManagement.Country
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Country1a As Model.AddressManagement.Country
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Country2 As Model.AddressManagement.Country
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Country3 As Model.AddressManagement.Country
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Country4 As Model.AddressManagement.Country
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Country5 As Model.AddressManagement.Country
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private CountryNothing As Model.AddressManagement.Country
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private CountryList As List(Of Model.AddressManagement.Country)
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private otherObject As New TextBox()
#End Region

#Region " Setup tests "
        ''' &lt;summary&gt;
        ''' Setup the private members
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;SetUp()&gt; _
        Public Sub Setup() Implements Util.Interfaces.ITest.Setup
            Country1 = New Model.AddressManagement.Country()
            Country1a = New Model.AddressManagement.Country()
            Country2 = New Model.AddressManagement.Country("Country 2", New Model.Common.Translation(), "")
            Country3 = New Model.AddressManagement.Country("Country 3", New Model.Common.Translation(), "")
            Country4 = New Model.AddressManagement.Country("Country 4", New Model.Common.Translation(), "")
            Country5 = New Model.AddressManagement.Country("Country 5", New Model.Common.Translation(), "")
            CountryList = New List(Of Model.AddressManagement.Country)
        End Sub
#End Region

#Region " Tests "
        ''' &lt;summary&gt;
        ''' Test Compareto
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test(Description:="Test Compareto")&gt; _
        Public Sub TestCompareTo() Implements Util.Interfaces.ITest.TestCompareTo
            CountryList.Add(Country1)
            CountryList.Add(Country1a)
            CountryList.Add(Country2)
            CountryList.Add(Country3)
            CountryList.Add(Country4)
            CountryList.Add(Country5)
            CountryList.Add(CountryNothing)
            CountryList.Reverse()
            Assert.AreNotEqual(Country5, CountryList(0))
            Assert.AreNotEqual(Country1, CountryList(0))
            Assert.AreNotEqual(Country1a, CountryList(0))
            Assert.AreNotEqual(Country2, CountryList(0))
            Assert.AreNotEqual(Country3, CountryList(0))
            Assert.AreNotEqual(Country4, CountryList(0))
            Assert.AreEqual(CountryNothing, CountryList(0))
            Assert.AreEqual(Country5, CountryList(1))
            Assert.AreNotEqual(Country1, CountryList(1))
            Assert.AreNotEqual(Country1a, CountryList(1))
            Assert.AreNotEqual(Country2, CountryList(1))
            Assert.AreNotEqual(Country3, CountryList(1))
            Assert.AreNotEqual(Country4, CountryList(1))
            Assert.AreNotEqual(CountryNothing, CountryList(1))
            Assert.AreEqual(Country4, CountryList(2))
            Assert.AreNotEqual(Country1, CountryList(2))
            Assert.AreNotEqual(Country1a, CountryList(2))
            Assert.AreNotEqual(Country2, CountryList(2))
            Assert.AreNotEqual(Country3, CountryList(2))
            Assert.AreNotEqual(Country5, CountryList(2))
            Assert.AreNotEqual(CountryNothing, CountryList(2))
            Assert.AreEqual(Country3, CountryList(3))
            Assert.AreNotEqual(Country1, CountryList(3))
            Assert.AreNotEqual(Country1a, CountryList(3))
            Assert.AreNotEqual(Country2, CountryList(3))
            Assert.AreNotEqual(Country5, CountryList(3))
            Assert.AreNotEqual(Country4, CountryList(3))
            Assert.AreNotEqual(CountryNothing, CountryList(3))
            Assert.AreEqual(Country2, CountryList(4))
            Assert.AreNotEqual(Country1, CountryList(4))
            Assert.AreNotEqual(Country1a, CountryList(4))
            Assert.AreNotEqual(Country5, CountryList(4))
            Assert.AreNotEqual(Country3, CountryList(4))
            Assert.AreNotEqual(Country4, CountryList(4))
            Assert.AreNotEqual(CountryNothing, CountryList(4))
            Assert.AreEqual(Country1, CountryList(5))
            Assert.AreEqual(Country1a, CountryList(5))
            Assert.AreNotEqual(Country5, CountryList(5))
            Assert.AreNotEqual(Country2, CountryList(5))
            Assert.AreNotEqual(Country3, CountryList(5))
            Assert.AreNotEqual(Country4, CountryList(5))
            Assert.AreNotEqual(CountryNothing, CountryList(5))
            Assert.AreEqual(Country1a, CountryList(6))
            Assert.AreEqual(Country1, CountryList(6))
            Assert.AreNotEqual(Country5, CountryList(6))
            Assert.AreNotEqual(Country2, CountryList(6))
            Assert.AreNotEqual(Country3, CountryList(6))
            Assert.AreNotEqual(Country4, CountryList(6))
            Assert.AreNotEqual(CountryNothing, CountryList(6))
            CountryList.Sort()
            Assert.AreNotEqual(Country1, CountryList(0))
            Assert.AreNotEqual(Country1a, CountryList(0))
            Assert.AreNotEqual(Country2, CountryList(0))
            Assert.AreNotEqual(Country3, CountryList(0))
            Assert.AreNotEqual(Country4, CountryList(0))
            Assert.AreNotEqual(Country5, CountryList(0))
            Assert.AreEqual(CountryNothing, CountryList(0))
            Assert.AreEqual(Country1, CountryList(1))
            Assert.AreEqual(Country1a, CountryList(1))
            Assert.AreNotEqual(Country2, CountryList(1))
            Assert.AreNotEqual(Country3, CountryList(1))
            Assert.AreNotEqual(Country4, CountryList(1))
            Assert.AreNotEqual(Country5, CountryList(1))
            Assert.AreNotEqual(CountryNothing, CountryList(1))
            Assert.AreEqual(Country1, CountryList(2))
            Assert.AreEqual(Country1a, CountryList(2))
            Assert.AreNotEqual(Country2, CountryList(2))
            Assert.AreNotEqual(Country3, CountryList(2))
            Assert.AreNotEqual(Country4, CountryList(2))
            Assert.AreNotEqual(Country5, CountryList(2))
            Assert.AreNotEqual(CountryNothing, CountryList(2))
            Assert.AreNotEqual(Country1, CountryList(3))
            Assert.AreNotEqual(Country1a, CountryList(3))
            Assert.AreEqual(Country2, CountryList(3))
            Assert.AreNotEqual(Country3, CountryList(3))
            Assert.AreNotEqual(Country4, CountryList(3))
            Assert.AreNotEqual(Country5, CountryList(3))
            Assert.AreNotEqual(CountryNothing, CountryList(3))
            Assert.AreNotEqual(Country1, CountryList(4))
            Assert.AreNotEqual(Country1a, CountryList(4))
            Assert.AreNotEqual(Country2, CountryList(4))
            Assert.AreEqual(Country3, CountryList(4))
            Assert.AreNotEqual(Country4, CountryList(4))
            Assert.AreNotEqual(Country5, CountryList(4))
            Assert.AreNotEqual(CountryNothing, CountryList(4))
            Assert.AreNotEqual(Country1, CountryList(5))
            Assert.AreNotEqual(Country1a, CountryList(5))
            Assert.AreNotEqual(Country2, CountryList(5))
            Assert.AreNotEqual(Country3, CountryList(5))
            Assert.AreEqual(Country4, CountryList(5))
            Assert.AreNotEqual(Country5, CountryList(5))
            Assert.AreNotEqual(CountryNothing, CountryList(5))
            Assert.AreNotEqual(Country1, CountryList(6))
            Assert.AreNotEqual(Country1a, CountryList(6))
            Assert.AreNotEqual(Country2, CountryList(6))
            Assert.AreNotEqual(Country3, CountryList(6))
            Assert.AreNotEqual(Country4, CountryList(6))
            Assert.AreEqual(Country5, CountryList(6))
            Assert.AreNotEqual(CountryNothing, CountryList(6))
            Assert.AreEqual(-1, Country1.CompareTo(otherObject))
        End Sub

        ''' &lt;summary&gt;
        ''' Test Constructors
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test(Description:="Test Constructors")&gt; _
        Public Sub TestConstructors() Implements Util.Interfaces.ITest.TestConstructors
            Assert.AreEqual("", Country1.Remarks)
            Assert.AreEqual("", Country1.Name)
            Assert.IsNull(Country1.Id)
            Assert.IsNotNull(Country1.Description)
            Assert.AreEqual("", Country2.Remarks)
            Assert.AreEqual("Country 2", Country2.Name)
            Assert.IsNull(Country2.Id)
            Assert.IsNotNull(Country2.Description)
        End Sub

        ''' &lt;summary&gt;
        ''' Test Equals
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test(Description:="Test Equals")&gt; _
        Public Sub TestEquals() Implements Util.Interfaces.ITest.TestEquals
            Assert.AreEqual(True, Country1.Equals(Country1))
            Assert.AreEqual(True, Country1.Equals(Country1a))
            Assert.AreEqual(True, Country1a.Equals(Country1))
            Assert.AreEqual(True, Country1a.Equals(Country1a))
            Assert.AreEqual(True, Country2.Equals(Country2))
            Assert.AreEqual(True, Country3.Equals(Country3))
            Assert.AreEqual(True, Country4.Equals(Country4))
            Assert.AreEqual(True, Country5.Equals(Country5))
            Assert.AreEqual(False, Country1.Equals(Country2))
            Assert.AreEqual(False, Country1.Equals(Country3))
            Assert.AreEqual(False, Country1.Equals(Country4))
            Assert.AreEqual(False, Country1.Equals(Country5))
            Assert.AreEqual(False, Country1.Equals(CountryNothing))
            Assert.AreEqual(False, Country1.Equals(otherObject))
            Assert.AreEqual(False, Country1a.Equals(Country2))
            Assert.AreEqual(False, Country1a.Equals(Country3))
            Assert.AreEqual(False, Country1a.Equals(Country4))
            Assert.AreEqual(False, Country1a.Equals(Country5))
            Assert.AreEqual(False, Country1a.Equals(CountryNothing))
            Assert.AreEqual(False, Country1a.Equals(otherObject))
            Assert.AreEqual(False, Country2.Equals(Country1))
            Assert.AreEqual(False, Country2.Equals(Country1a))
            Assert.AreEqual(False, Country2.Equals(Country3))
            Assert.AreEqual(False, Country2.Equals(Country4))
            Assert.AreEqual(False, Country2.Equals(Country5))
            Assert.AreEqual(False, Country2.Equals(CountryNothing))
            Assert.AreEqual(False, Country2.Equals(otherObject))
            Assert.AreEqual(False, Country3.Equals(Country1))
            Assert.AreEqual(False, Country3.Equals(Country1a))
            Assert.AreEqual(False, Country3.Equals(Country2))
            Assert.AreEqual(False, Country3.Equals(Country4))
            Assert.AreEqual(False, Country3.Equals(Country5))
            Assert.AreEqual(False, Country3.Equals(CountryNothing))
            Assert.AreEqual(False, Country3.Equals(otherObject))
            Assert.AreEqual(False, Country4.Equals(Country1))
            Assert.AreEqual(False, Country4.Equals(Country1a))
            Assert.AreEqual(False, Country4.Equals(Country2))
            Assert.AreEqual(False, Country4.Equals(Country3))
            Assert.AreEqual(False, Country4.Equals(Country5))
            Assert.AreEqual(False, Country4.Equals(CountryNothing))
            Assert.AreEqual(False, Country4.Equals(otherObject))
            Assert.AreEqual(False, Country5.Equals(Country1))
            Assert.AreEqual(False, Country5.Equals(Country1a))
            Assert.AreEqual(False, Country5.Equals(Country2))
            Assert.AreEqual(False, Country5.Equals(Country3))
            Assert.AreEqual(False, Country5.Equals(Country4))
            Assert.AreEqual(False, Country5.Equals(CountryNothing))
            Assert.AreEqual(False, Country5.Equals(otherObject))
        End Sub

        ''' &lt;summary&gt;
        ''' Test Properties
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test(Description:="Test properties")&gt; _
        Public Sub TestProperties() Implements Util.Interfaces.ITest.TestProperties
            Country1.Description = New Model.Common.Translation("en", "nl", "fr")
            Country1.Name = "Country 1"
            Country1.Remarks = "Remarks"
            Assert.AreEqual("en", Country1.Description.En)
            Assert.AreEqual("fr", Country1.Description.Fr)
            Assert.AreEqual("nl", Country1.Description.Nl)
            Assert.AreEqual("Country 1", Country1.Name)
            Assert.AreEqual("Remarks", Country1.Remarks)
            Assert.IsNull(Country1.Id)
        End Sub

        ''' &lt;summary&gt;
        ''' Test ToString
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test(Description:="Test ToString")&gt; _
        Public Sub TestToString() Implements Util.Interfaces.ITest.TestToString
            Assert.AreEqual(True, Country1.ToString.StartsWith("[Name: "))
            Assert.AreEqual(True, Country2.ToString.StartsWith("[Name: "))
            Assert.AreEqual(True, Country3.ToString.StartsWith("[Name: "))
            Assert.AreEqual(True, Country4.ToString.StartsWith("[Name: "))
            Assert.AreEqual(True, Country5.ToString.StartsWith("[Name: "))
        End Sub

        &lt;Test()&gt; _
        Public Sub TestIFIsDirtyIsFalseAfterNew()
            Dim _Country As New Model.AddressManagement.Country()
            Assert.AreEqual(False, _Country.IsDirty)
            _Country = New Model.AddressManagement.Country("test")
            Assert.AreEqual(False, _Country.IsDirty)
            _Country = New Model.AddressManagement.Country("test", Nothing, "")
            Assert.AreEqual(False, _Country.IsDirty)
        End Sub

        &lt;Test()&gt; _
        Public Sub TestIfIsDirtyIsTruAfterUpdatePropertyCountry()
            Dim _Country As New Model.AddressManagement.Country()
            Assert.AreEqual(False, _Country.IsDirty)
            _Country.Name = "Test"
            Assert.AreEqual(True, _Country.IsDirty)
        End Sub
#End Region

    End Class
End Namespace
```
Yep a whole lot more code. Read it and ask questions if needed.

 [1]: http://www.nunit.org/index.php
 [2]: http://www.jetbrains.com/resharper/
 [3]: /index.php/DesktopDev/MSTech/domainentity-and-isdirty