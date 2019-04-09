---
title: Check your MSN status from ASP.NET
author: ca8msm
type: post
date: 2009-03-21T18:46:07+00:00
ID: 360
url: /index.php/webdev/webdesigngraphicsstyling/check-your-msn-status-from-asp-net/
views:
  - 13349
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Web Design, Graphics and Styling
  - XHTML and CSS

---
I decided that I wanted to provide a “live chat” link on my website so I had a look around for what software I could use to do it. There were quite a few good options, but none that were good enough for me and free. So, I had a look into using my MSN account which turned out to be quite easy.

First off, you will need to sign in and go to:

http://settings.messenger.live.com/applications/WebSettings.aspx

Then, check the box that says “Allow anyone on the web to see my presence and send me messages.”:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/MSN1.gif" alt="" title="" width="751" height="217" />
</div>

Then, go to the “Create HTML” link on the left hand side of the page and choose “Status icon” from the “Choose which control you want to display on your page” section:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/MSN2.gif" alt="" title="" width="628" height="392" />
</div>

You will then be shown a small snippet of HTML that you can use to enter onto your webpage:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/MSN3.gif" alt="" title="" width="713" height="134" />
</div>

What we need to do is to use the URL from the “src” attribute in the above link, and in my case it looked like:
  
_
  
http&#58;&#47;&#47;messenger.services.live.com/users/338469ac4a1e779a@apps.messenger.live.com/presenceimage?mkt=en-GB_

I only ever wanted to show whether I was online or not (I wasn't bothered about the other status' such as busy, away, in a call etc) so I thought an easy way of getting my status was to use the image returned in the above URL and see whether it was the grey looking icon (which meant I was offline) or one of the other green type ones that meant I was online.

To do this, I added a few imports:

```vbnet
Imports System.Net
Imports System.IO
Imports System.Drawing
```
Then, I made a fairly simple function to grab the image and check how many colours were in the image (it's a greyscale image that is shown if you're offline, so a low amount means that is your status):

```vbnet
Public Function IsOnline() As Boolean
        Dim strURL As String
        Dim wbrq As HttpWebRequest
        Dim wbrs As HttpWebResponse

        ' Set the URL (and add any querystring values)  
        strURL = "http://messenger.services.live.com/users/b2bec1cffdfed491@apps.messenger.live.com/presenceimage?mkt=en-GB"

        ' Create the web request  
        wbrq = WebRequest.Create(strURL)
        wbrq.Method = "GET"

        ' Read the returned bitmap object and return the status   
        wbrs = wbrq.GetResponse
        Dim b As New Bitmap(wbrs.GetResponseStream)
        If b.Palette.Entries.GetLength(0) > 64 Then
            Return True
        Else
            Return False
        End If
    End Function
```
So, as you can see from above, if the number of entries is less than 64, this dictates that I'm offline. Anything else, and I'm one of the other status' which as far as I'm concerned in this case makes me online and available.

To see it in action, have a look at the “Live Chat” link in the footer of [my website][1].

 [1]: http://www.mdssolutions.co.uk