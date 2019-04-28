---
title: Trying out RavenDB with VB.Net and images
author: Christiaan Baes (chrissie1)
type: post
date: 2011-04-24T05:34:00+00:00
ID: 1138
excerpt: |
  Introduction
  
  So yesterday I started to play with RavenDB and today I wanted to add images to my document database. 
  
  Testing
  
  So first thing I did was to adapt my model.
  
  Namespace Model
      Public Class Plant
          Public Property Id As St&hellip;
url: /index.php/desktopdev/mstech/trying-out-ravendb-with-vb-1/
views:
  - 2296
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - ravendb
  - vb.net

---
## Introduction

So [yesterday I started to play with RavenDB][1] and today I wanted to add images to my document database. 

## Testing

So first thing I did was to adapt my model.

```vbnet
Namespace Model
    Public Class Plant
        Public Property Id As String
        Public Property LatinName As String
        Public Property EnglishNames As ISet(Of String)
        Public Property FlowerColor As String
        Public Property PlantImage As Image
    End Class
End Namespace```
And then I added a test.

```vbnet
&lt;Test()&gt;
        Public Sub CanAddImageToPlantAndRetrieveIt()
            Dim service = New PlantService(RavenDBSession.GetTestSession)
            service.AddPLant(New Plant With {.PlantImage = Image.FromFile("Testsfilesfagus.jpg")})
            Dim plant = service.GetPlant("1")
            Assert.IsNotNull(plant.PlantImage)
        End Sub```
Since I prefer for this to be a bit more lifelike I choose to copy an image to my project and see if I can load that. 

And yes that worked. Easy as pie.

## For real

Of course the test just shows me I have something there but now I need to try if I can show that image too. So time to hook up a winform. 

So I created a form.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ravendb/RavenDB1.PNG?mtime=1303629797"><img alt="" src="/wp-content/uploads/users/chrissie1/ravendb/RavenDB1.PNG?mtime=1303629797" width="438" height="382" /></a>
</div>

And added some code to it.

```vbnet
Imports Plants.Model
Imports Plants.Services

Public Class Form1

    Private _service As PlantService

    Public Sub New(ByVal service As PlantService)
        InitializeComponent()
        _service = service
    End Sub


    Private Sub btnBrowse_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnBrowse.Click
        Dim _OpenDialog1 As New OpenFileDialog
        If _OpenDialog1.ShowDialog = Windows.Forms.DialogResult.OK Then
            picPlantImage.Image = Image.FromFile(_OpenDialog1.FileName)
        End If
    End Sub

    Private Sub btnSave_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnSave.Click
        _service.AddPLant(New Plant() With {.LatinName = txtLatinName.Text, .PlantImage = picPlantImage.Image})
    End Sub
    
    Private Sub btnLoad_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnLoad.Click
        Dim plant = _service.GetPlant("1")
        txtLatinName.Text = plant.LatinName
        picPlantImage.Image = plant.PlantImage
        txtNumberOfPlants.Text = _service.NumberOfPlants.ToString
    End Sub

    Private Sub btnEmpty_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnEmpty.Click
        txtLatinName.Text = ""
        picPlantImage.Image = Nothing
        txtNumberOfPlants.Text = ""
    End Sub
End Class
```
And some startup code so I can inject the service.

```vbnet
Imports Plants.Services

Module Startup
    Public Sub Main()
        Dim form As New Form1(New PlantService(RavenDBSession.GetTestSession))
        Application.Run(form)
    End Sub
End Module```
And this is what it looks like with some data filled in.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ravendb/RavenDB2.PNG?mtime=1303630161"><img alt="" src="/wp-content/uploads/users/chrissie1/ravendb/RavenDB2.PNG?mtime=1303630161" width="438" height="382" /></a>
</div>

I then click Empty and get this again.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ravendb/RavenDB1.PNG?mtime=1303629797"><img alt="" src="/wp-content/uploads/users/chrissie1/ravendb/RavenDB1.PNG?mtime=1303629797" width="438" height="382" /></a>
</div>

And then we load it.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ravendb/RavenDB3.PNG?mtime=1303630308"><img alt="" src="/wp-content/uploads/users/chrissie1/ravendb/RavenDB3.PNG?mtime=1303630308" width="438" height="382" /></a>
</div>

And now we see we have 1 plant in our database. 

## Conclusion

It was very easy to add an image to my plant and save it to the database.

 [1]: /index.php/DesktopDev/MSTech/trying-out-ravendb-with-vb