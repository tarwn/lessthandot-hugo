---
title: nHibernate performance against Stored procedures part 2
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-24T05:05:42+00:00
ID: 92
url: /index.php/desktopdev/mstech/nhibernate-performance-against-stored-pr-2/
views:
  - 3400
categories:
  - Microsoft Technologies

---
### Index

[Prelude][1]
  
[Part 1][2]
  
[Part 2][3]
  
[Part 3][4]
  
[Conclusion][5]
  


* * *

Yes The stored procedures came to town.

Here is the first one.

```tsql
CREATE PROC spo_getMspCoordinates
AS
SELECT TOP 1000000 id, a, WaveLenghtinnm
FROM dbo.tbl_MSPCoordinate

GO
```
This just gets the first million out of that database.

Now we hit a snag and something that kills performance. But I can&#8217;t think of a reasonable way around it without using dynamic sql.

So here it is.

```tsql
CREATE PROC spo_getMspCoordinatesTop
@top VARCHAR(30)
AS
Declare @SQL VarChar(1000)

SELECT @SQL = 'SELECT TOP ' + @top + ' id, a, WaveLenghtinnm' 
SELECT @SQL = @SQL + ' FROM dbo.tbl_MSPCoordinate'

Exec ( @SQL)

GO
```
And here are the tests.

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
        Public Sub Select_MSP_SQLClientProcedures_1000000()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("sqlclientprocedure")
            Assert.IsNotNull(_crud.Selectall)
            Assert.Greater(_crud.Selectall.Count, 0)
            Assert.AreEqual(1000000, _crud.Selectall.Count)
        End Sub

        &lt;Test()&gt; _
        Public Sub Select_MSP_SQLClientProcedures_1000()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("sqlclientprocedure")
            Assert.IsNotNull(_crud.Selectall(1000))
            Assert.Greater(_crud.Selectall(1000).Count, 0)
            Assert.AreEqual(1000, _crud.Selectall(1000).Count)
        End Sub

        &lt;Test()&gt; _
        Public Sub Select_MSP_SQLClientProcedures_1000_1000()
            Dim _crud As DAL.Interfaces.IMSPCoordinate = StructureMap.ObjectFactory.GetNamedInstance(Of DAL.Interfaces.IMSPCoordinate)("sqlclientprocedure")
            For i As Integer = 0 To 999
                Assert.IsNotNull(_crud.Selectall(1000))
                Assert.Greater(_crud.Selectall(1000).Count, 0)
                Assert.AreEqual(1000, _crud.Selectall(1000).Count)
            Next
        End Sub
    End Class
End Namespace```
And here is the implementation

```vbnet
Imports StructureMap

Namespace DAL.CRUD.SQLClientTransaction
    &lt;Pluggable("sqlclientprocedure")&gt; _
    Public Class MSPCoordinate
        Implements Interfaces.IMSPCoordinate

        Public Function Selectall() As System.Collections.Generic.IList(Of Model.MspCoordinate) Implements Interfaces.IMSPCoordinate.Selectall
            Dim _returnlist As New List(Of Model.MspCoordinate)
            Dim c As New Data.SqlClient.SqlConnection("Data Source=incctex;Initial Catalog=testtexdatabase;Integrated Security=SSPI;")
            Dim s As New Data.SqlClient.SqlCommand()
            c.Open()
            s.Connection = c
            s.CommandType = CommandType.StoredProcedure
            s.CommandText = "spo_getMspCoordinates"
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
            Dim s As New Data.SqlClient.SqlCommand()
            c.Open()
            s.Connection = c
            s.CommandType = CommandType.StoredProcedure
            s.CommandText = "spo_getMspCoordinatesTop"
            s.Parameters.Add(New Data.SqlClient.SqlParameter("top", SqlDbType.VarChar, 30))
            s.Parameters(0).Value = top.ToString
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
And here are the results.

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
      Select_MSP_SQLClientTransactions_1000000
    </td>
    
    <td align="right">
      7.53 s
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientTransactions_1000
    </td>
    
    <td align="right">
      0.04 s
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientTransactions_1000_1000
    </td>
    
    <td align="right">
      10.51 s
    </td>
  </tr>
</table>

Yes the Dynamic sql is killing it. But not more then the &#8220;inline&#8221; sql.

<span style="color:red;">Edit</span>

With the new Stored procedure, without dynamic sql.

```tsql
ALTER PROC spo_getMspCoordinatesTop
@TOP INT
AS
SET ROWCOUNT @TOP
SELECT id, a, WaveLenghtinnm FROM dbo.tbl_MSPCoordinate```
The numbers become this.

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
      Select_MSP_SQLClientTransactions_1000000
    </td>
    
    <td align="right">
      7.32 s
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientTransactions_1000
    </td>
    
    <td align="right">
      0.09 s
    </td>
  </tr>
  
  <tr>
    <td>
      Select_MSP_SQLClientTransactions_1000_1000
    </td>
    
    <td align="right">
      9.39 s
    </td>
  </tr>
</table>

Not much of an improvement, but we take what we can get.
  
So dynamic sql isn&#8217;t all that bad after all :>

* * *

<font color="Red">Need help with VB.Net? Come and ask a question in our <a href="http://forum.ltd.local/viewforum.php?f=39">VB.Net Forum</a></font>

 [1]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr
 [2]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-1
 [3]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-2
 [4]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-3
 [5]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-4