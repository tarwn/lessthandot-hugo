---
title: Monodevelop and Nancy and VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2014-06-24T12:59:40+00:00
ID: 2793
excerpt: My first website with monodevelop and nancy.
url: /index.php/uncategorized/monodevelop-and-nancy-and-vb-net/
views:
  - 3643
categories:
  - Uncategorized

---
This morning I wrote on [how to install monodevelop][1] and now I will make my first website using monodevelop.

First thing to do when you work with monodevelop is to install the [nuget-plugin][2].

Now create a VB.Net ConsolApplication.

And then we can go look for Nancy.Hosting.Self via Project > Add Packages 

[<img src="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-144858-300x197.png" alt="Screenshot from 2014-06-24 14:48:58" width="300" height="197" class="alignnone size-medium wp-image-2794" srcset="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-144858-300x197.png 300w, /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-144858.png 820w" sizes="(max-width: 300px) 100vw, 300px" />][3]

Add that to your project. 

And now it is time for some code.

First we make sure we start our host. In the Application.vb file.<pre lang=vbnet> Public Class Application Public Shared Sub Main() Dim host = New Nancy.Hosting.Self.NancyHost(New Uri("http://localhost:1234")) host.Start() Console.ReadLine() End Sub End Class </pre> 

And then we add an EmptyFile and add this code to it.<pre lang=vbnet> Public Class SampleModule Inherits Nancy.NancyModule public Sub New() Me.Get("/") = AddressOf Hello End Sub Public Function Hello(ByVal parameters as Object) As String Return "Hello world" End Function End Class </pre> 

And now open a browser and go to http://localhost:1234/

And magic.

[<img src="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-145441-300x221.png" alt="Screenshot from 2014-06-24 14:54:41" width="300" height="221" class="alignnone size-medium wp-image-2795" srcset="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-145441-300x221.png 300w, /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-145441.png 423w" sizes="(max-width: 300px) 100vw, 300px" />][4]

Did you see the no frills VB.Net? It work but it is lightyears behind VB.Net on windows.

 [1]: /index.php/uncategorized/monodevelop-and-vb-net-and-ubuntu-how-to-install/
 [2]: http://community.sharpdevelop.net/blogs/mattward/archive/2013/01/07/MonoDevelopNuGetAddin.aspx
 [3]: /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-144858.png
 [4]: /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-145441.png