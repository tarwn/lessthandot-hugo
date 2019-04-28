---
title: Domainentity and IsDirty
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-06T05:58:07+00:00
ID: 45
url: /index.php/desktopdev/mstech/domainentity-and-isdirty/
views:
  - 1984
categories:
  - Microsoft Technologies

---
I never saw the need to add isdirty to a domainentity untill today.

After showing the user a list of objects on the screen in a datagridview (which I don&#8217;t use very often) I then had to save that list. And there are two ways to go about it. Let nHibernate handle it or pick out the changed objects. Since the nhibernate route was a bit slow. I decided to do it myself and add an IsDirty to the Domain object. 

First of all why haven&#8217;t I done this before? Because I think a domainobject doesn&#8217;t need to concern itself with those kinds of things it is there to enforce the bussiness rules and for nothing else. But I think I can compromise. Since all the Domainobjects have a corresponding interface I can make Different Domainobjects for differnt purposses. I can have one for nhibernate with the attributes I can have a simple one and I can have one that inherits from the DomainEntity superclass. I really couldn&#8217;t care less since the view uses the interface and doesn&#8217;t know about the implementation. 

So here is the IDomainEntity interface.

```
Namespace Common.Interfaces
    ''' &lt;summary&gt;
    ''' Interface for our Superclass DomainEntity
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    Public Interface IDomainEntity
        ''' &lt;summary&gt;
        ''' Tells me when an object has been changed
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        ReadOnly Property IsDirty() As Boolean
        ''' &lt;summary&gt;
        ''' Clears the isdirtystate
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Sub ClearDirtyStatus()
    End Interface
End Namespace```
And this is the implementation

```
Namespace Common
    ''' &lt;summary&gt;
    ''' Superclass DomainEntity which holds the Isdirty logic
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    Public MustInherit Class DomainEntity
        Implements Interfaces.IDomainEntity

#Region " Private members "
        ''' &lt;summary&gt;
        ''' Private member to hold a boolean saying when my object is dirty
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _IsDirty As Boolean
#End Region

#Region " Constructors "
        ''' &lt;summary&gt;
        ''' Empty constructor 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New()

        End Sub
#End Region

#Region " Public properties "
        ''' &lt;summary&gt;
        ''' Tells me when an object has been changed
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;true when the object has been changed.&lt;/returns&gt;
        ''' &lt;remarks&gt;Sets IsDirty to false&lt;/remarks&gt;
        Public ReadOnly Property IsDirty() As Boolean Implements Interfaces.IDomainEntity.IsDirty
            Get
                Return _IsDirty
            End Get
        End Property
#End Region

#Region " Public methods "
        ''' &lt;summary&gt;
        ''' Clears the isdirtystate
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub ClearDirtyStatus() Implements Interfaces.IDomainEntity.ClearDirtyStatus
            _IsDirty = False
        End Sub

        ''' &lt;summary&gt;
        ''' Protectd method to be used by the inheriting classes.
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;Sets IsDirty to true&lt;/remarks&gt;
        Protected Sub SetDirty()
            _IsDirty = True
        End Sub
#End Region

    End Class
End Namespace
```
Don&#8217;t hold back on the comments, but be gentle I&#8217;m a nice guy afterall. And I know about CSLA.NET.