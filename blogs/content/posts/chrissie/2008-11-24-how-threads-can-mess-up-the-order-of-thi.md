---
title: How threads can mess up the order of things.
author: Christiaan Baes (chrissie1)
type: post
date: 2008-11-24T11:40:54+00:00
ID: 217
url: /index.php/desktopdev/mstech/how-threads-can-mess-up-the-order-of-thi/
views:
  - 4126
categories:
  - Microsoft Technologies
tags:
  - order
  - thread
  - threading

---
Today I was trying to print a bunch of reports. The reports were supposed to come out in order because the are numbered. 

You can do this in one report I hear you say&#8230; no because I use an old version of a reporting engine and that engine doesn&#8217;t really support multipage reports very well. You see I need to print a dutch version on one side and the french version on the other side. I could do this by printing all the dutch first and then feeding them back in and printing the french side, but dutch and french version need to have the same number obviously. Not so obvious if the printer has a hickup from time to time. But we now have a duplex printer so I just have to send out page 1 in dutch and then page 2 in french with the same number then page 3 dutch again with the following number. This is something my version of the reporting engine can&#8217;t handle but I had a quick fix. Making it print one page dutch and then a page french with the same number can be done in the same report. So I just send out n-times such a report with a changing number. Simple.

This seemed very easy and I had it printing all the reports in notime at all. But they came out in random order. It took me a while to remember how that came. I wrote the offending code a long time ago.

First I started by ordering the list I used. Then I started to debug the for each loop that prints all of them. And everything was fine.

Then I stumbled accross this.

I use the same reporting class for all my reporting. And the printreport method looks like this.

```vbnet
Public Sub PrintReport(ByVal Dialog As Boolean) Implements Interfaces.INineraysReport.PrintReport
            _Dialog = Dialog
            _PrintThread.Start()
        End Sub```
Do you notice anything?

Yes it uses a thread. So my calling method did have the list sorted like it should have been but the thread messed everything up.

So I have to make it not use the thread for this. Or have the thread just wait for eachother which will give about the same result.

I guess the user will just have to wait until I send the reports to the printer.

So I changed it to this.

```vbnet
Public Sub PrintReport(ByVal Dialog As Boolean, Optional ByVal UseThread As Boolean = True) Implements Interfaces.INineraysReport.PrintReport
            _Dialog = Dialog
            If UseThread Then
                _PrintThread.Start()
            Else
                PrintReport()
            End If
        End Sub```
PrintReport is a private method BTW that does all the hard work.