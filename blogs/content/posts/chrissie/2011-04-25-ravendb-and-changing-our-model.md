---
title: RavenDB and changing our model
author: Christiaan Baes (chrissie1)
type: post
date: 2011-04-25T05:55:00+00:00
ID: 1139
excerpt: |
  Introduction
  
  I have been messing with RavenDB over the past few days. Nothing serious, just getting a feel for it.
  
    Trying out RavenDB with VB.Net
    Trying out RavenDB with VB.Net and images
  
  
  And I wanted to know what happens when I change m&hellip;
url: /index.php/desktopdev/mstech/ravendb-and-changing-our-model/
views:
  - 1935
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - ravendb
  - vb.net

---
## Introduction

I have been messing with RavenDB over the past few days. Nothing serious, just getting a feel for it.

  * [Trying out RavenDB with VB.Net][1]
  * [Trying out RavenDB with VB.Net and images][2]

And I wanted to know what happens when I change my model, add properties and remove properties. I know I expected these two to just work but one always has to test it&#8217;s theories and not take things for granted.

For this I am no longer going to use the in memory server but the http one. Which you use like this.

```vbnet
Public Shared Function GetNewSession() As IDocumentSession
            _store = New DocumentStore With {.Url = "http://localhost:8080"}
            _store.Initialize()
            Return _store.OpenSession
        End Function```
and I have to start the server by running Raven.server.exe in the server folder of your RavenDB download.

## Adding a property

I first add a plant to my database and then change the model to this.

```vbnet
Public Class Plant
        Public Property Id As String
        Public Property LatinName As String
        Public Property Familyname As String
        Public Property EnglishNames As ISet(Of String)
        Public Property FlowerColor As String
        Public Property PlantImage As Image
    End Class```
I have added a property called family name to the model and then I added another. I can now check my 2 new documents in RavenDB by going browsing to http://localhost:8080 you will need to have silverlight installed and remember that silverlight does not run on a 64x browser.

And here is the result. I just added 2 docs. One with family name and one without.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB4.PNG?mtime=1303716059"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB4.PNG?mtime=1303716059" width="822" height="300" /></a>
</div>

So I adapted the form I used in the previous post to see what happens when I load plants/1 and plants/1025. And here are the results.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB5.PNG?mtime=1303716948"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB5.PNG?mtime=1303716948" width="438" height="261" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB6.PNG?mtime=1303716959"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB6.PNG?mtime=1303716959" width="438" height="261" /></a>
</div>

It just works. No problem there.

## Removing a property

So now I can just remove that property again. Add another plant and see if I can load them again.

I now have another plant with id 2049. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB7.PNG?mtime=1303717345"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB7.PNG?mtime=1303717345" width="762" height="155" /></a>
</div>

I can now show these docs in my form, I will just have to remove the family name property usage.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB8.PNG?mtime=1303717657"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB8.PNG?mtime=1303717657" width="438" height="261" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB9.PNG?mtime=1303717672"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB9.PNG?mtime=1303717672" width="438" height="261" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB10.PNG?mtime=1303717698"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravendb/RavenDB10.PNG?mtime=1303717698" width="438" height="261" /></a>
</div>

And that just works.

## Conclusion

I can just change my schema/model without any trouble. I guess that is one of the good things about these document databases. If I was using an RDBMS I would have to not only change my model but also the schema of my database.

 [1]: /index.php/DesktopDev/MSTech/trying-out-ravendb-with-vb
 [2]: /index.php/DesktopDev/MSTech/trying-out-ravendb-with-vb-1