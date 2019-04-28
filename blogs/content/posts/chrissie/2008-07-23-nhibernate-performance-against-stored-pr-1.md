---
title: nHibernate performance against Stored procedures part 1
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-23T12:26:48+00:00
ID: 91
url: /index.php/desktopdev/mstech/nhibernate-performance-against-stored-pr-1/
views:
  - 5119
categories:
  - Microsoft Technologies
  - VB.NET

---
### Index

[Prelude][1]
  
[Part 1][2]
  
[Part 2][3]
  
[Part 3][4]
  
[Conclusion][5]
  


* * *

So like I promised I would write a little comparison. 

First: Is this really useful? **No**, not really just an interesting exercise. Because in the end performance isn&#8217;t everything.

So here we go.

First I created a table. 

```tsql
if exists (select * from dbo.sysobjects where id = object_id(N'[dbo].[tbl_MSPCoordinate]') and OBJECTPROPERTY(id, N'IsUserTable') = 1)
drop table [dbo].[tbl_MSPCoordinate]
GO

CREATE TABLE [dbo].[tbl_MSPCoordinate] (
	[Id] [bigint] IDENTITY (1, 1) NOT NULL ,
	[MSP_id] [varchar] (36) COLLATE Latin1_General_CI_AS NULL ,
	[WaveLenghtinnm] [decimal](19, 5) NOT NULL ,
	[A] [decimal](19, 5) NOT NULL ,
	[Added_By] [varchar] (20) COLLATE Latin1_General_CI_AS NULL ,
	[Added_On] [datetime] NULL 
) ON [PRIMARY]
GO```
Well I already had this and it happens to be filled with rows. Several million to be exact.

Then I created my class. MSPCoordinate.

```vbnet
Imports System.Text
Imports NHibernate.Mapping.Attributes

Namespace Model
    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
        &lt;[Class](0, table:="tbl_MSPCoordinate", lazy:=False)&gt; _
Public Class MspCoordinate
        
#Region " Private members "

        ''' &lt;summary&gt;
        ''' A local variable called _id of type String
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;Has Property Id&lt;/remarks&gt;
        Private _id As Int64

        ''' &lt;summary&gt;
        ''' A local variable called _a of type Decimal
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;Has Property A&lt;/remarks&gt;
        Private _a As Decimal

        ''' &lt;summary&gt;
        ''' A local variable called _waveLenghtinnm of type Decimal
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;Has Property WaveLenghtinnm&lt;/remarks&gt;
        Private _waveLenghtinnm As Decimal

#End Region

#Region " Constructors "

        ''' &lt;summary&gt;
        ''' Default empty constructor
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New()
            Me.New(0, 0, 0)
        End Sub

        ''' &lt;summary&gt;
        ''' Constructor with all the fields
        ''' &lt;/summary&gt;
        ''' &lt;param name="A"&gt;DataType = Decimal&lt;/param&gt;
        ''' &lt;param name="WaveLenghtinnm"&gt;DataType = Decimal&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New(ByVal Id As Int64, ByVal A As Decimal, ByVal WaveLenghtinnm As Decimal)
            Me.Id = Id
            Me.A = A
            Me.WaveLenghtinnm = WaveLenghtinnm
        End Sub

#End Region

#Region " Public properties "

        ''' &lt;summary&gt;
        ''' This is the Id property.
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;String&lt;/value&gt;
        ''' &lt;returns&gt;String&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Id(0, Name:="Id") _
            , Generator(1, Class:="native")&gt; _
        Public Overridable Property Id() As Int64
            Get
                Return _id
            End Get
            Set(ByVal Value As Int64)
                _id = Value
            End Set
        End Property

        ''' &lt;summary&gt;
        ''' This is the A property.
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;Decimal&lt;/value&gt;
        ''' &lt;returns&gt;Decimal&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;[Property]()&gt; _
        Public Overridable Property A() As Decimal
            Get
                Return _a
            End Get
            Set(ByVal Value As Decimal)
                _a = Value
            End Set
        End Property

        ''' &lt;summary&gt;
        ''' This is the WaveLenghtinnm property.
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;Decimal&lt;/value&gt;
        ''' &lt;returns&gt;Decimal&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;[Property]()&gt; _
        Public Overridable Property WaveLenghtinnm() As Decimal
            Get
                Return _waveLenghtinnm
            End Get
            Set(ByVal Value As Decimal)
                _waveLenghtinnm = Value
            End Set
        End Property

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Overridable ReadOnly Property A_absorption() As Decimal
            Get
                If _a = 0 Then
                    Return 0
                Else
                    If Math.Log10(_a / 100) * -1 &gt; Decimal.MaxValue Then
                        Return Decimal.MaxValue
                    ElseIf Math.Log10(_a / 100) * -1 &lt; Decimal.MinValue Then
                        Return Decimal.MinValue
                    End If
                End If
                Return Convert.ToDecimal(Math.Log10(_a / 100) * -1)
            End Get
        End Property

#End Region

#Region " Overrides ToString "

        ''' &lt;summary&gt;
        ''' Overridden ToString method for this class
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;String&lt;/returns&gt;
        ''' &lt;remarks&gt;The String value of this class represented by the ToStrings of all it's private members&lt;/remarks&gt;
        Public Overrides Function ToString() As String
            Dim ReturnString As New StringBuilder()
            ReturnString.Append("[Id: " & _id.ToString() & "]")
            ReturnString.Append(" - [A: " & _a.ToString() & "]")
            ReturnString.Append(" - [WaveLenghtinnm: " & _waveLenghtinnm.ToString() & "]")
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
                    Return Me._waveLenghtinnm.Equals(CType(obj, MspCoordinate)._waveLenghtinnm)
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
        Public Function CompareTo(ByVal obj As Object) As Integer
            If obj IsNot Nothing Then
                If obj.GetType Is Me.GetType Then
                    Return Me._waveLenghtinnm.CompareTo(CType(obj, MspCoordinate)._waveLenghtinnm)
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
Yes, that was there too. Not in this form but something similar.

Then I had to create a Dal to make life easy.

First the interface.

```vbnet
Imports StructureMap

Namespace DAL.Interfaces
    &lt;PluginFamily("nhibernate", issingleton:=True)&gt; _
    Public Interface IMSPCoordinate
        Function Selectall() As IList(Of Model.MspCoordinate)
        Function Selectall(ByVal top As Integer) As IList(Of Model.MspCoordinate)
    End Interface
End Namespace```
Look at the structuremap beauty, yes, I still use the attributes.

And the first tests

```vbnet
Imports NUnit.Framework
Imports StructureMap

Namespace Test
    &lt;TestFixture()&gt; _
    Public Class TestSpeedNHibernate

        &lt;SetUp()&gt; _
        Public Sub Setup()
            StructureMapConfiguration.UseDefaultStructureMapConfigFile = False
            StructureMapConfiguration.ScanAssemblies.IncludeTheCallingAssembly()

        End Sub

        &lt;Test()&gt; _
        Public Sub Select_MSP_SQLClient_1000000_2()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("sqlclient")
            Assert.IsNotNull(_crud.Selectall)
            Assert.Greater(_crud.Selectall.Count, 0)
            Assert.AreEqual(1000000, _crud.Selectall.Count)
        End Sub

        &lt;Test()&gt; _
        Public Sub Select_MSP_SQLClient_1000()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("sqlclient")
            Assert.IsNotNull(_crud.Selectall(1000))
            Assert.Greater(_crud.Selectall(1000).Count, 0)
            Assert.AreEqual(1000, _crud.Selectall(1000).Count)
        End Sub

        &lt;Test()&gt; _
        Public Sub Select_MSP_SQLClient_1000_1000()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("sqlclient")
            For i As Integer = 0 To 999
                Assert.IsNotNull(_crud.Selectall(1000))
                Assert.Greater(_crud.Selectall(1000).Count, 0)
                Assert.AreEqual(1000, _crud.Selectall(1000).Count)
            Next
        End Sub

    End Class
End Namespace```
And then the first implementation of this.

```vbnet
Imports StructureMap

Namespace DAL.CRUD.SQLClient
    &lt;Pluggable("sqlclient")&gt; _
    Public Class MSPCoordinate
        Implements Interfaces.IMSPCoordinate

        Public Function Selectall() As System.Collections.Generic.IList(Of Model.MspCoordinate) Implements Interfaces.IMSPCoordinate.Selectall
            Dim _returnlist As New List(Of Model.MspCoordinate)
            Dim c As New Data.SqlClient.SqlConnection("Data Source=incctex;Initial Catalog=testtexdatabase;Integrated Security=SSPI;")
            Dim s As New Data.SqlClient.SqlCommand("select top 1000000 Id, A, wavelenghtinnm from tbl_mspcoordinate", c)
            c.Open()
            Dim r As Data.SqlClient.SqlDataReader
            r = s.ExecuteReader
            While r.Read
                _returnlist.Add(New Model.MspCoordinate(Convert.ToInt64(r.GetValue(0)), r.GetDecimal(1), r.GetDecimal(2)))
            End While
            s.Dispose()
            r.Close()
            c.Close()
            c.Dispose()
            Return _returnlist
        End Function

        Public Function Selectall1(ByVal top As Integer) As System.Collections.Generic.IList(Of Model.MspCoordinate) Implements Interfaces.IMSPCoordinate.Selectall
            Dim _returnlist As New List(Of Model.MspCoordinate)
            Dim c As New Data.SqlClient.SqlConnection("Data Source=incctex;Initial Catalog=testtexdatabase;Integrated Security=SSPI;")
            Dim s As New Data.SqlClient.SqlCommand("select top " & top.ToString & " Id, A, wavelenghtinnm from tbl_mspcoordinate", c)
            c.Open()
            Dim r As Data.SqlClient.SqlDataReader
            r = s.ExecuteReader
            While r.Read
                _returnlist.Add(New Model.MspCoordinate(Convert.ToInt64(r.GetValue(0)), r.GetDecimal(1), r.GetDecimal(2)))
            End While
            s.Dispose()
            r.Close()
            c.Close()
            c.Dispose()
            Return _returnlist
        End Function
    End Class
End Namespace```
And these are the results according to resharper unit test thing.

<table border="1">
  <tr>
    <th width="400">
      Test
    </th>
    
    <th width="100">
      Time
    </th>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClient_1000000
    </td>
    
    <td align="right">
      9.00 s
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClient_1000000_2
    </td>
    
    <td align="right">
      8.06 s
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClient_1000
    </td>
    
    <td align="right">
      0.01 s
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClient_1000_1000
    </td>
    
    <td align="right">
      10.36 s
    </td>
  </tr>
</table>

Looks reasonable to me. It isn&#8217;t the fastest server in the world after all.

Next installment the stored procedure route and a little snag along the way.

* * *

<font color="Red">Need help with VB.Net? Come and ask a question in our <a href="http://forum.ltd.local/viewforum.php?f=39">VB.Net Forum</a></font>

 [1]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr
 [2]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-1
 [3]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-2
 [4]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-3
 [5]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-4