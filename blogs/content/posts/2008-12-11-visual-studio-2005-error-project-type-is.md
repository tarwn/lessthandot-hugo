---
title: Visual Studio 2005 error – “Project type is not supported by this installation”
author: ca8msm
type: post
date: 2008-12-11T09:27:33+00:00
ID: 246
excerpt: |
  I recently had to go back and make some changes to a Web Service I'd made a while ago in VS2005. For some reason, when I tried to open the project, Visual Studio gave me this error:
  
  "Project type is not supported by this installation"
  
  I can't thin&hellip;
url: /index.php/webdev/serverprogramming/aspnet/visual-studio-2005-error-project-type-is/
views:
  - 13671
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET

---
I recently had to go back and make some changes to a Web Service I'd made a while ago in VS2005. For some reason, when I tried to open the project, Visual Studio gave me this error:

_“Project type is not supported by this installation”_

I can't think of any changes I've made to my Visual Studio installation, but _something_ must have changed for it to no longer see the type of project I'd created (it could open other projects in the solution, just not this particular one).

Anyway, after much searching and a few failed attempts to fix it from other posts I'd read on the subject, here's what worked for me:

1. Close all instances of Visual Studio

2. Download and install “Microsoft Visual Studio 2005 – Update to Support Web Application Projects” from:

http://www.microsoft.com/downloads/details.aspx?FamilyId=8B05EE00-9554-4733-8725-3CA89DD9BFCA&displaylang=en

3. Download and install “Web Application Project Setup” from

http://download.microsoft.com/download/9/0/6/906064ce-0bd1-4328-af40-49dca1aef87c/WebApplicationProjectSetup.msi

4. Restart Visual Studio

And now my project now opens correctly 😀