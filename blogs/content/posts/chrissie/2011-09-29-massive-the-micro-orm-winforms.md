---
title: Massive (The Micro-ORM), winforms and VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-29T07:58:00+00:00
ID: 1332
excerpt: "I've been using nHibernate for quite a few years now and have been having plenty of success with it. But sometimes it's just to much. So today I had a little project I had to do and I decided I would take Massive from Rob Connery a spin."
url: /index.php/desktopdev/mstech/massive-the-micro-orm-winforms/
views:
  - 5915
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - massive
  - vb.net

---
## Introduction

I&#8217;ve been using nHibernate for quite a few years now and have been having plenty of success with it. But sometimes it&#8217;s just to much. So today I had a little project I had to do and I decided I would take [Massive][1] from Rob Connery a spin.

## Install

Well, there is no install. Massive is just a gigantic C# file that you would insert into your project if you were using C#. Of course you can&#8217;t do that in VB.Net. For VB.Net you just create a new C# Class Library project and call it Massive, no need to be adventurous here. And then you just add a reference to that. 

You can now use nuget to add it to your project or just copy pates the [Massive.cs][2] file from github or use git of course to do it for you. Plenty of choices.

## Configuration

Configuration of Massive is easy. It just needs a connectionstring. 

You set the connectionstring in your app.config of your main library and if you have a testproject don&#8217;t forget to copy it there too.

The app.config would look something like this.

```xml
&lt;?xml version="1.0"?&gt;
&lt;configuration&gt;
    &lt;connectionStrings&gt;
      &lt;add name="msplog" 
           connectionString="yourconnectionstring" 
           providerName="System.Data.SqlClient"/&gt;
    &lt;/connectionStrings&gt;
    &lt;startup&gt;
        &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/&gt;
    &lt;/startup&gt;
&lt;/configuration&gt;```
I&#8217;m sure you know what bits to chance to make that work.

## The database

I made one table in my database.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/massive/massive1.png?mtime=1317289570"><img alt="" src="/wp-content/uploads/users/chrissie1/massive/massive1.png?mtime=1317289570" width="336" height="200" /></a>
</div>

## Getting the data

And finally we have arrived at the part we need.

First we need to create a Class that inherits from DynamicModel for our Table. We can do this inline or make it a seperate class.

This is the seperate class.

```vbnet
Imports Massive

Namespace Services.MassiveModel

    Public Class MspLog
        Inherits DynamicModel

        Public Sub New()
            MyBase.new("MspLog", "tbl_MspLog", "Id")
        End Sub

    End Class
End Namespace```
I gave three parameters to the constructor. The first one is the name of the connectionstring I want it to use. If you don&#8217;t specify that it will just pack the first one. The second is the name of the table and the third is the name of the primary key field I used.

And then I can just query that.

```vbnet
Dim tbl = New MassiveModel.MspLog
Dim logs = tbl.All(orderBy:="DateLog DESC")```
AS you can see I returned all objects from the table and ordered them by DateLog in a descending order.

<span class="MT_red">Massive uses dynamic objects to return it&#8217;s data so you will need to turn Option Strict Off.</span> 

And then I can just show those objects to my users.

```vbnet
For Each log In logs
  Console.WriteLine(log.UserName)
  Console.WriteLine(log.LogDate.ToString)
Next
```
And that is all there is to it.

## Conclusion

Massive is also usable if you use winforms and VB.Net. And Massive is pretty simple to use.

 [1]: https://github.com/robconery/massive
 [2]: https://github.com/robconery/massive/blob/master/Massive.cs