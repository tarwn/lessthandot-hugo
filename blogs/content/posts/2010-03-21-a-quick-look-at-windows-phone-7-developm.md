---
title: A quick look at Windows Phone 7 development
author: SQLDenis
type: post
date: 2010-03-21T17:25:13+00:00
ID: 732
url: /index.php/itprofessionals/ethicsit/a-quick-look-at-windows-phone-7-developm/
views:
  - 11380
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
tags:
  - silverlight
  - windows phone
  - xna studio
  - zune hd

---
Today I decided to take a look at Windows Phone 7 development. The first thing I did was download the Windows Phone Developer Tools, you can download it here: http://www.microsoft.com/express/phone/

Windows Phone Developer Tools includes:

  * Visual Studio 2010 Express for Windows Phone
  * Windows Phone Emulator
  * Silverlight for Windows Phone
  * XNA Game Studio 4.0 CTP

The download was 228 MB for me, I already had Visual Studio 2008 and Visual Studio 2010 RC on my machine, if you don't have those, the download might be bigger.

<img src="/wp-content/uploads/blogs/ITProfessionals//IPhoneSetup.png" alt="" title="" width="516" height="464" />

Here are the 10 things that will be downloaded and installed.

_Visual Studio 2010 Express
  
Silverlight 4
  
Silverlight 4 SDK
  
Microsoft Windows Phone Emulator x64
  
Microsoft Windows Phone Developer Resources
  
Windows Phone 7 Add-in for Visual Studio 2010
  
Microsoft Windows Phone Developers Tools CTP
  
Microsoft XNA Game Studio 4.0
  
Microsoft XNA Game Studio 4.0 Windows.....
  
Silverlight 4 Tools for Visual Studio 2010 RC_

After everything is downloaded and installed, you will see the following window
  
<img src="/wp-content/uploads/blogs/ITProfessionals//Phone7Done.png" alt="" title="" width="516" height="464" />
  


## Visual Studio 2010 and Windows Phone 7 development

Now that we are done, we can start playing around with the tools.
  
The way you develop apps/games for Windows Phone 7 is that you either use Silverlight 4 or XNA Studio 4
  
If you pick Silverlight for Windows Phone then the following templates are available
  
<img src="/wp-content/uploads/blogs/ITProfessionals//PhoneSilverLight.png" alt="" title="Windows Phone 7 Silverlight" width="845" height="560" />
  

  
If you pick XNA Game Studio 4.0 then the following templates are available
  
<img src="/wp-content/uploads/blogs/ITProfessionals//PhoneXNA.png" alt="" title="Windows Phone 7 XNA Studio" width="845" height="560" />

I decided to pick the Silverlight Windows Phone List Application.
  
After messing around with it for 15 minutes or so I managed to create the following "app". I put app in quotes because it really doesn't do anything yet, it is all static. Hopefully I will be able to grab all the XML feeds we have on this site and plug it into the app. I myself have a Zune HD and I have heard that these apps should also work on the Zune HD.

Below is an image of the "app" in action

<img src="/wp-content/uploads/blogs/ITProfessionals//PhoneApp.png" alt="" title="" width="292" height="570" />
  


I also shot a 2 minute and 11 second video where I play around with the "app" and also click around in Visual Studio to show you some of the stuff.

Below is a video of the "app" in action [video:youtube:e7SHq4w17D0] 

## Learning about Windows Phone 7 development

I did all the stuff in this post without having looked at any documentation at all, I have never done any Silverlight development. To me it seems that developing for Windows Phone is not that difficult. Here are some resources for you if you want to learn more about Windows Phone 7 development. I will take a look at these also over the next couple of weeks and hopefully that "app" I created will be able to pull in real live data.
  
Charles Petzold is writing a book titled Programming Windows Phone 7 Series, right now you can download the draft preview of that book.

This preview ebook contains six chapters in three parts (153 pages total):

Part I Getting Started
  
Chapter 1 Phone Hardware + Your Software
  
Chapter 2 Hello, Windows Phone

Part II Silverlight
  
Chapter 3 Code and XAML
  
Chapter 4 Presentation and Layout

Part III XNA
  
Chapter 5 Principles of Movement
  
Chapter 6 Textures and Sprites

Download this book here: [Programming Windows Phone 7 Series(DRAFT Preview)][1]

Here are some more resources to learn about Windows Phone development
  
[Windows Phone Developer Portal][2]
  
[Getting Started Guide for Developing for Windows Phone][3]
  
[Code Samples for Windows Phone][4] 
  
[Windows Phone 7 Series Developer Training Kit][5]

That is all for this post, as mentioned before I will take a look at those resources myself and will see if I can make that app have more functionality soon.

 [1]: http://ow.ly/1lhdc
 [2]: http://developer.windowsphone.com/
 [3]: http://msdn.microsoft.com/en-us/library/ff402529(VS.92).aspx
 [4]: http://msdn.microsoft.com/en-us/library/ff431744(VS.92).aspx
 [5]: http://channel9.msdn.com/learn/courses/WP7TrainingKit/