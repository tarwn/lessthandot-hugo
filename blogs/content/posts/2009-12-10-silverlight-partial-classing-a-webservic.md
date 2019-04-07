---
title: Silverlight Partial Classing a Webservice Reference
author: ThatRickGuy
type: post
date: 2009-12-10T13:39:11+00:00
ID: 644
url: /index.php/webdev/webdesigngraphicsstyling/silverlight-partial-classing-a-webservic/
views:
  - 8666
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - silverlight partial class webservice

---
Hey Folks,

<p style="text-indent: 30pt;">
  Just ran into this minor quirk today, figured I&#8217;d share the solution. I have a Webservice that returns an array of a class defined on the server. On the client side, I wanted to extend that class to link it to some GUI elements. Enter the partial class. If you haven&#8217;t played with Partial Classes before, I&#8217;d recommend checking them out. It allows you to build additional functionality into any class. Not by inheriting from a base, but actually extending that class without creating a new one. Anyways, I wound up with the following:
</p>

```vbnet
Namespace srAlerts
    Partial Public Class Alert

        Private _CommentDisplay As CommentIndicator
        <System.Xml.Serialization.XmlIgnore()> _
        Public Property CommentDisplay() As CommentIndicator
            Get
                Return _CommentDisplay
            End Get
            Set(ByVal value As CommentIndicator)
                _CommentDisplay = value
            End Set
        End Property

    End Class
End Namespace
```
<p style="text-indent: 30pt;">
  srAlerts is the namespace of my web service. Alert is the name of the class defined in my web service. CommentIndicatory is the GUI control that I was linking to.
</p>

<p style="text-indent: 30pt;">
  The critical part though, is the attribute. Without the System.Xml.Serialization.XmlIgnore attribute, you&#8217;ll get an exception on the constructor of the web service Reference.vb file:
</p>

Exception has been thrown by the target of an invocation.
  
-There was an error reflecting type &#8216;Alerts.srAlerts.BaseResponse&#8217;.
  
&#8211;There was an error reflecting type &#8216;Alerts.srAlerts.GetAlertDataResponse&#8217;.
  
&#8212;There was an error reflecting property &#8216;Alerts&#8217;.
  
&#8212;-There was an error reflecting type &#8216;Alerts.srAlerts.Alert&#8217;.
  
&#8212;&#8211;There was an error reflecting property &#8216;CommentDisplay&#8217;.

<p style="text-indent: 30pt;">
  It would appear that the web service system in Silverlight attempts to serialize all members of the class, even those that are defined client side in a partial class, and it gets a little confused. Using the XMLIgnore attribute forces the serializer to skip this property and allows us to use the partial class on the client side as we expected.
</p>

<p style="text-indent: 30pt;">
  On a side note, there is also a WCF attribute: System.Runtime.Serialization.IgnoreDataMember which will NOT work for web service references.
</p>

-Rick