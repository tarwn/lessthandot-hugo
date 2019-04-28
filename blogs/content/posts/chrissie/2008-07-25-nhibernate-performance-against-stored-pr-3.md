---
title: nHibernate performance against Stored procedures part 3
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-25T04:51:14+00:00
ID: 93
url: /index.php/desktopdev/mstech/nhibernate-performance-against-stored-pr-3/
views:
  - 3354
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

This is going to be another lengthy post. But you can skip all the code bits and go to the end. Or wait a bit and read the conclusion in the last post.

First, nHibernate needs some sort of configuration

```vbnet
Imports NHibernate.Mapping.Attributes
Imports NHibernate
Imports NHibernate.Cfg
Imports StructureMap

Namespace DAL.CRUD.Hibernate
    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
        &lt;Pluggable("Live")&gt; _
Public Class NHibernateConfiguration

#Region " Private members "

        ''' &lt;summary&gt;
        ''' Holds the Configuration so that the subclasses can use it.
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;Of type Nhibernate.Cfg.Configuration.&lt;/remarks&gt;
        Private _Configuration As Configuration

        ''' &lt;summary&gt;
        ''' Holds the sessionfactory so that the sub classes can use it.
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;is an interface of type Nhibernate.ISessionFactory&lt;/remarks&gt;
        Private _SessionFactory As ISessionFactory

        ''' &lt;summary&gt;
        ''' Holds the Databaseserver so we can easily switch between Test and productionservers.
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;is of type string and will be used in the connectionstring
        ''' &lt;/remarks&gt;
        Private _ServerName As String

        ''' &lt;summary&gt;
        ''' Holds the Database so we can easily switch between Test and productiondatabases.
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _Database As String

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _SessionFactoryString As String

#End Region

#Region " Constructors "

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New()
            MakeConfiguration()
        End Sub

#End Region

#Region " Public properties "

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public ReadOnly Property Configuration() As Configuration
            Get
                If _Configuration Is Nothing Then
                    MakeConfiguration()
                End If
                Return _Configuration
            End Get
        End Property

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public ReadOnly Property SessionFactory() As ISessionFactory
            Get
                If _Configuration Is Nothing Then
                    MakeConfiguration()
                End If
                If _SessionFactory Is Nothing Then
                    MakeSessionFactory()
                End If
                Return _SessionFactory
            End Get
        End Property

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public ReadOnly Property SessionFactoryString() As String
            Get
                Return _SessionFactoryString
            End Get
        End Property

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public ReadOnly Property Database() As String
            Get
                Return _Database
            End Get
        End Property

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public ReadOnly Property ServerName() As String
            Get
                Return _ServerName
            End Get
        End Property

#End Region

#Region " Private methods "

        ''' &lt;summary&gt;
        ''' Makes the configuration and sets the property
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Sub MakeConfiguration()
            Dim _DBObjectSettings As New DBObjectSettings()
            _ServerName = "Server"
            _Database = "Database"
            _Configuration = New Configuration()
            _Configuration.SetProperty(Environment.ConnectionProvider, _DBObjectSettings.ConnectionProvider)
            _Configuration.SetProperty(Environment.Dialect, _DBObjectSettings.Dialect)
            _Configuration.SetProperty(Environment.ConnectionDriver, _DBObjectSettings.ConnectionDriver)
            _Configuration.SetProperty(Environment.ConnectionString, String.Format(_DBObjectSettings.ConnectionString, _ServerName, _Database))
        End Sub

        ''' &lt;summary&gt;
        ''' Makes the sessionfactory
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Sub MakeSessionFactory()
            Try
                Dim _DBObjectSettings As New DBObjectSettings()
                HbmSerializer.Default.HbmDefaultAccess = _DBObjectSettings.HbmDefaultAccess
                HbmSerializer.Default.Validate = True
                _Configuration.AddInputStream(HbmSerializer.Default.Serialize(System.Reflection.Assembly.GetExecutingAssembly()))
            Catch ex As Exception
                Console.WriteLine(ex.Message)
            End Try
            Try
                _SessionFactory = _Configuration.BuildSessionFactory()
            Catch ex As Exception
                Console.WriteLine(ex.Message)
            End Try
        End Sub

#End Region

    End Class
End Namespace```
Then it needs some additional tests.

```vbnet
&lt;Test()&gt; _
        Public Sub Select_MSP_SQLClientnhibernate_1000000()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("nhibernate")
            Assert.IsNotNull(_crud.Selectall)
            Assert.Greater(_crud.Selectall.Count, 0)
            Assert.AreEqual(1000000, _crud.Selectall.Count)
        End Sub

        &lt;Test()&gt; _
        Public Sub Select_MSP_SQLClientnhibernate_1000()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("nhibernate")
            Assert.IsNotNull(_crud.Selectall(1000))
            Assert.Greater(_crud.Selectall(1000).Count, 0)
            Assert.AreEqual(1000, _crud.Selectall(1000).Count)
        End Sub

        &lt;Test()&gt; _
        Public Sub Select_MSP_SQLClientnhibernate_1000_1000()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("nhibernate")
            For i As Integer = 0 To 999
                Assert.IsNotNull(_crud.Selectall(1000))
                Assert.Greater(_crud.Selectall(1000).Count, 0)
                Assert.AreEqual(1000, _crud.Selectall(1000).Count)
            Next
        End Sub```
Then it needs some implementation.

```vbnet
Imports NHibernate
Imports StructureMap

Namespace DAL.CRUD.Hibernate
    &lt;Pluggable("nhibernate")&gt; _
    Public Class MSPCoordinate
        Implements Interfaces.IMSPCoordinate

        ''' &lt;summary&gt;
        ''' Holds the sessionfactory so that the sub classes can use it.
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;is an interface of type Nhibernate.ISessionFactory&lt;/remarks&gt;
        Protected SessionFactory As ISessionFactory

        Public Sub New()
            SessionFactory = New NHibernateConfiguration().SessionFactory
        End Sub

        Public Function Selectall() As System.Collections.Generic.IList(Of Model.MspCoordinate) Implements Interfaces.IMSPCoordinate.Selectall
            Dim _Session As ISession = Nothing
            Dim _ReturnList As IList(Of Model.MspCoordinate) = Nothing
            _Session = SessionFactory.OpenSession
            _ReturnList = _Session.CreateQuery("FROM MspCoordinate").SetMaxResults(1000000).List(Of Model.MspCoordinate)()
            If _Session IsNot Nothing Then
                _Session.Close()
                _Session.Dispose()
            End If
            Return _ReturnList
        End Function

        Public Function Selectall1(ByVal top As Integer) As System.Collections.Generic.IList(Of Model.MspCoordinate) Implements Interfaces.IMSPCoordinate.Selectall
            Dim _Session As ISession = Nothing
            Dim _ReturnList As IList(Of Model.MspCoordinate) = Nothing
            _Session = SessionFactory.OpenSession
            Dim q As IQuery
            q = _Session.CreateQuery("FROM MspCoordinate")
            q.SetMaxResults(top)
            _ReturnList = q.List(Of Model.MspCoordinate)()
            If _Session IsNot Nothing Then
                _Session.Close()
                _Session.Dispose()
            End If
            Return _ReturnList
        End Function
    End Class
End Namespace```
And then the times.

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
      Select_MSP_SQLClientnhibernate_1000000
    </td>
    
    <td align="right">
      2:08.34 m
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientnhibernate_1000
    </td>
    
    <td align="right">
      0:00.09 m
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientnhibernate_1000_1000
    </td>
    
    <td align="right">
      1:35.81 m
    </td>
  </tr>
</table>

I also tried this with a transaction, but as can be expected with a select of this type, it didn&#8217;t help much.

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
      Select_MSP_SQLClientnhibernatetrans_1000000
    </td>
    
    <td align="right">
      2:48.07 m
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientnhibernatetrans_1000
    </td>
    
    <td align="right">
      0:00.15 m
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientnhibernatetrans_1000_1000
    </td>
    
    <td align="right">
      2:14.03 m
    </td>
  </tr>
</table>

And the last thing I tried was with createcriteria instead of createquery.

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
      Select_MSP_SQLClientnhibernatecrit_1000000
    </td>
    
    <td align="right">
      2:08.21 m
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientnhibernatecrit_1000
    </td>
    
    <td align="right">
      0:00.25 m
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientnhibernatecrit_1000_1000
    </td>
    
    <td align="right">
      1:36.35 m
    </td>
  </tr>
</table>

* * *

<font color="Red">Need help with VB.Net? Come and ask a question in our <a href="http://forum.ltd.local/viewforum.php?f=39">VB.Net Forum</a></font>

 [1]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr
 [2]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-1
 [3]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-2
 [4]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-3
 [5]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-4