---
title: Programatically create a sharepoint calendar entry
author: ca8msm
type: post
date: 2010-06-02T07:50:19+00:00
ID: 805
excerpt: "We recently had a requirement to programatically create an entry in an existing Sharepoint calendar, so here is an example of how we went about doing this.I'll be using Visual Studio 2008 for this, but the process will similar for any other versions you are using. First of all, you will need to add a reference to one of the Sharepoint web references, so right-click your project and select Add Web Reference."
url: /index.php/webdev/serverprogramming/programatically-create-a-sharepoint-cale/
views:
  - 14584
rp4wp_auto_linked:
  - 1
categories:
  - Server Programming
tags:
  - .net
  - calendar
  - sharepoint

---
We recently had a requirement to programatically create an entry in an existing Sharepoint calendar, so here is an example of how we went about doing this.

I&#8217;ll be using Visual Studio 2008 for this, but the process will be similar for any other versions you are using. First of all, you will need to add a reference to one of the Sharepoint web references, so right-click your project and select Add Web Reference. You&#8217;ll then be presented with a screen that asks you for a URL for that web service, so go ahead and write it in the following format:

http://nis/\_vti\_bin/lists.asmx

NOTE: You&#8217;ll need to replace the &#8220;nis&#8221; section with the path to your own Sharepoint website.

The following screen will then be presented displaying the list of available methods:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/SharepointCalendars/AddWebReference.png" alt="" title="" width="817" height="563" />
</div>

Change the Web Reference Name to &#8220;Sharepoint.Lists&#8221; and click the &#8220;Add Reference&#8221; button to add it to your project.

Next, you&#8217;ll need to open up a new form/page/module (whichever is appropriate to your solution), add a few imports:

```vbnet
Imports Sharepoint
Imports System.Text
Imports System.XML
```

and then we&#8217;ll create a function that builds an XML string ready to be passed to the Sharepoint web service:

```vbnet
Public Function CreateCalendarEntry(ByVal CalendarName As String, ByVal Title As String, ByVal Description As String, ByVal AddToDate As DateTime, ByVal FullDay As Boolean, ByVal LengthInMinutes As Double) As XmlNode

        ' Declarations
        Dim sBatch As New StringBuilder

        ' Get a reference to the list web service
        Dim listService As New Lists
        listService.Credentials = System.Net.CredentialCache.DefaultCredentials

        ' Create the XML to be passed to the calendar
        sBatch.Append("<Method ID='1' Cmd='New'>")
        sBatch.Append("<Field Name='Title'>" & Title & "</Field>")
        If FullDay = True Then
            sBatch.Append("<Field Name='EventDate'>" & AddToDate.ToString("yyyy-MM-dd") & "</Field>")
            sBatch.Append("<Field Name='EndDate'>" & AddToDate.ToString("yyyy-MM-dd") & "</Field>")
            sBatch.Append("<Field Name='fAllDayEvent'>1</Field>")
        Else
            sBatch.Append("<Field Name='EventDate'>" & AddToDate.ToString("yyyy-MM-ddTHH:mm:ssZ") & "</Field>")
            sBatch.Append("<Field Name='EndDate'>" & AddToDate.AddMinutes(LengthInMinutes).ToString("yyyy-MM-ddTHH:mm:ssZ") & "</Field>")
            sBatch.Append("<Field Name='fAllDayEvent'>0</Field>")
        End If
        sBatch.Append("<Field Name='Description'>" & Description & "</Field>")
        sBatch.Append("</Method>")

        ' Add the calendar XML to a batch
        Dim xmlDoc2 As New System.Xml.XmlDocument()
        Dim Batch As System.Xml.XmlElement = xmlDoc2.CreateElement("Batch")
        Batch.InnerXml = sBatch.ToString

        ' Pass the XML to the webservice and return the result
        Return listService.UpdateListItems(CalendarName, Batch)

    End Function
```

As you can see from the above, we build an XML string based upon:

1. The name of the calendar
  
2. A title
  
3. A description
  
4. When it needs to be entered into the calendar (i.e on a certain date, for a full day or a certain amount of hours)

We then pass the XML to the web service which will then insert the calendar entry and return a resulting XML string for you to check it&#8217;s success.

So, as an example call to the service, if you pass in some test data:

```vbnet
MyTestPage.CreateCalendarEntry("Development Team Calendar", "LessThanDot.com", "This is a sample calendar entry", System.DateTime.Now, False, 30)
```

then you should see an example entry in your calendar:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/SharepointCalendars/CalendarEntry.png" alt="" title="" width="622" height="258" />
</div>

along with some further details once you click into the actual entry:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/SharepointCalendars/CalendarEntryDetails.png" alt="" title="" width="650" height="298" />
</div>