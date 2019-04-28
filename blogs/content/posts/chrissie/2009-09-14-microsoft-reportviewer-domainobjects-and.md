---
title: 'Microsoft reportviewer: domainobjects and ReportDataSource needs a collection.'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-09-14T10:16:46+00:00
ID: 559
url: /index.php/desktopdev/mstech/microsoft-reportviewer-domainobjects-and/
views:
  - 4553
categories:
  - Microsoft Technologies
tags:
  - microsoft reporting engine
  - reportviewer
  - vb.net

---
I&#8217;m switching from a previous reporting engine to the microsoft reporting engine that comes free. No, I never used Crystal reports and I don&#8217;t know yet how much I&#8217;m gonna hate this one either. But the word free makes up for a lot in this world. And they all seem to have pros and cons. 

Okay So I wanted to use an objectdatasource since I work with domainobjects and I would have liked to make reports from them. The reportviewer has some little quirks with objects. They have made it with this in mind but not the first thing in mind and it shows a little.

Peter van Ooijen wrote a nice post [Reporting against a domain model][1] about how to do this and I followed it but still I had some things missing because it took me some time to get it working in the end.

I also wanted to make a report against an Invoice So I have an Invoice object and invoicelines. The invoicelines ar part of the invoice object as it should. So If I pass the invoice object to the report I already have the invoicelines that belong to them.

So the first thing I do is 

```vbnet
_viewer.LocalReport.DataSources.Add(New ReportDataSource("TDB2009_Dal_Model_CaseManagement_Invoice", Invoice))```
where invoice is of type Invoice 😉

ANd then you get this very to the point errormessage.

<span class="MT_red">Value does not fall within the expected range.</span>

Well someone thought that made sense tot hem anyway. 

After some searching I found that it needs a collection of some kind (I wonder then why you are allowed to pass an Object to it).

changing it tot this 

```vbnet
Dim l2 As New ArrayList()
l2.Add(Invoice)
_viewer.LocalReport.DataSources.Add(New ReportDataSource("TDB2009_Dal_Model_CaseManagement_Invoice", l2))```
made the pain go away. 

Ok On to the next problem.

 [1]: http://codebetter.com/blogs/peter.van.ooijen/archive/2009/07/01/reporting-against-a-domain-model.aspx