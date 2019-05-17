---
title: 'nHibernate performance against stored procedures: Conclusion'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-25T04:52:28+00:00
ID: 94
url: /index.php/desktopdev/mstech/nhibernate-performance-against-stored-pr-4/
views:
  - 6113
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

So here we are. We all saw the figures. Nothing we didn&#8217;t already know, so let&#8217;s not dwell on the obvious. NHibernate will always be slower than the rest. 

If you look at the figures and decide not to use NHibernate or any other ORM from now on, then go right ahead, you just missed the point altogether. You didn&#8217;t notice the rest of the design did you? You only looked at the numbers and they told you nothing. Do you really want to maintain an application that has a couple of hundred of those sqlclient methods? I don&#8217;t. Did you see how structuremap made it easy to switch from one implementation to the other? So, if needed, you can always write another one for speed. Did you notice how you could write a generic class with nHibernate to do the selectall? Probably not, but here it is.

First, I create a generic interface:

```vbnet
Namespace DAL.Interfaces
    Public Interface ICrudObject(Of T)
        Function Selectall() As IList(Of T)
        Function Selectall(ByVal top As Integer) As IList(Of T)
    End Interface
End Namespace```
Then I change the IMSPCoordinate version a little, without it affecting any of the other implementations.

```vbnet
Imports StructureMap

Namespace DAL.Interfaces
    &lt;PluginFamily("nhibernate", issingleton:=True)&gt; _
    Public Interface IMSPCoordinate
        Inherits ICrudObject(Of Model.MspCoordinate)
    End Interface
End Namespace```
Now we also need a crudobject.

```vbnet
Imports NHibernate

Namespace DAL.CRUD.Hibernate
    Public Class CrudObject(Of T)
        Implements Interfaces.ICrudObject(Of T)
        ''' &lt;summary&gt;
        ''' Holds the sessionfactory so that the sub classes can use it.
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;is an interface of type Nhibernate.ISessionFactory&lt;/remarks&gt;
        Protected SessionFactory As ISessionFactory

        Public Sub New()
            SessionFactory = New NHibernateConfiguration().SessionFactory
        End Sub

        Public Function SelectAll() As System.Collections.Generic.IList(Of T) Implements Interfaces.ICrudObject(Of T).Selectall
            Dim _Session As ISession = Nothing
            Dim _ReturnList As IList(Of T) = Nothing
            _Session = SessionFactory.OpenSession
            _ReturnList = _Session.CreateCriteria(GetType(T)).SetMaxResults(1000000).List(Of T)()
            If _Session IsNot Nothing Then
                _Session.Close()
                _Session.Dispose()
            End If
            Return _ReturnList
        End Function

        Public Function SelectAll(ByVal top As Integer) As System.Collections.Generic.IList(Of T) Implements Interfaces.ICrudObject(Of T).Selectall
            Dim _Session As ISession = Nothing
            Dim _ReturnList As IList(Of T) = Nothing
            _Session = SessionFactory.OpenSession
            _ReturnList = _Session.CreateCriteria(GetType(T)).SetMaxResults(top).List(Of T)()
            If _Session IsNot Nothing Then
                _Session.Close()
                _Session.Dispose()
            End If
            Return _ReturnList
        End Function
    End Class
End Namespace```
And this is what&#8217;s left of our implementation:

```vbnet
Imports NHibernate
Imports StructureMap

Namespace DAL.CRUD.Hibernate
    &lt;Pluggable("nhibernate")&gt; _
    Public Class MSPCoordinate
        Inherits CrudObject(Of Model.MspCoordinate)
        Implements Interfaces.IMSPCoordinate

        Public Sub New()
            MyBase.New()
        End Sub

    End Class
End Namespace```
That is a lot shorter, right? Now do you see the point? With nHibernate, you can add many classes to your model; writing the DAL will be fast and easy. If you need to do that with the sqlclients, you will have to write all kinds of support functions to minimize the amount of code. And what if a class changes property? In the end, you will start using reflection and all sorts of stuff and in the end, you start writing your own ORM. Don&#8217;t do that, use and existing one and learn from our mistakes.

So the real conclusion is, use the ORM as much as possible until performance is really needed, then write your own. Anyway, if you ever need one million records out of your database, then you should know that you did something wrong to begin with. What user is going to scroll through a list of 1 million rows? Google will tell you that most people get bored after a hundred.

* * *

<font color="Red">Need help with VB.Net? Come and ask a question in our <a href="http://forum.lessthandot.com/viewforum.php?f=39">VB.Net Forum</a></font>

 [1]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr
 [2]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-1
 [3]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-2
 [4]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-3
 [5]: /index.php/DesktopDev/MSTech/nhibernate-performance-against-stored-pr-4