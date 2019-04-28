---
title: Wrong mapping Component in nHibernate using VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-08T11:57:46+00:00
ID: 47
url: /index.php/desktopdev/mstech/wrong-mapping-component-in-nhibernate-us/
views:
  - 2880
categories:
  - Microsoft Technologies

---
Today I had a little problem with component mapping and the attributes. And It has something to do with the fact that all my objects use interfaces.

I had this in my MspResult class.

```
&lt;NHibernate.Mapping.Attributes.ComponentProperty(componenttype:=GetType(Littlexyz))&gt; _
        Public Overridable Property Littlexyz() As Interfaces.ILittleXyz Implements IMspResult.Littlexyz
            Get
                _Log.LogDebug("Property Littlexyz - returning _Littlexyz")
                Return _littlexyz
            End Get
            Set(ByVal Value As Interfaces.ILittleXyz)
                _Log.LogDebug("Property Littlexyz - setting _Littlexyz")
                _littlexyz = Value
            End Set
        End Property```
And this in the Littlexyz class.

```
&lt;NHibernate.Mapping.Attributes.Component())&gt; _
    Public Class Littlexyz
        Implements ILittleXyz

#Region " Private members "
        ''' &lt;summary&gt;
        ''' A local variable called _x of type Decimal
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;Has Property X&lt;/remarks&gt;
        Private _x As Decimal```
Which gave me this error in nhibernate

NHibernate.MappingException: Problem trying to set property type by reflection &#8212;> NHibernate.PropertyNotFoundException: Could not find field &#8216;_x&#8217; in class &#8216;Model.Msp.Interfaces.ILittleXyz&#8217;

Which is completely correct since the interface doesn&#8217;t have any private fields.
  
But the solutiion was simple 😉 as always it just took a little searching.
  
So i just changed the Littlexyz class to this.

```
&lt;NHibernate.Mapping.Attributes.Component(classtype:=GetType(Model.Msp.Littlexyz))&gt; _
    Public Class Littlexyz
        Implements ILittleXyz

#Region " Private members "
        ''' &lt;summary&gt;
        ''' A local variable called _x of type Decimal
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;Has Property X&lt;/remarks&gt;
        Private _x As Decimal```
Which does exactly what I want and what it should.