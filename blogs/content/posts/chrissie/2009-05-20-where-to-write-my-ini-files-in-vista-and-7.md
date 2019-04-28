---
title: Where to write my ini files in Vista and Windows 7
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-20T08:19:39+00:00
ID: 438
url: /index.php/desktopdev/mstech/where-to-write-my-ini-files-in-vista-and-7/
views:
  - 3123
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - vista
  - windows 7

---
All my users use Windows XP for now, they have no say in the matter. But me I use Vista 64bits. So I test my application on that. And I have a few VMs to test the windows XP behaviour and try and make them work on both. And sometimes you get to deal with the increased security in ista or Windows 7. For instance you are no longer allowed to write in the Program files folder. So I had to find another place to write my application specific ini file. I know I should have moved it long ago. But now is a good a time as any.
  
But apparently I can write things to the CommonApplicationData Folder.

```csharp
return Environment.OSVersion.Version.Major &lt;= 5 ? new System.IO.DirectoryInfo(Environment.GetFolderPath(Environment.SpecialFolder.ProgramFiles) + "\" + Settings.Default.Version) : new System.IO.DirectoryInfo(Environment.GetFolderPath(Environment.SpecialFolder.CommonApplicationData) + "\" + Settings.Default.Version);```
Which in Windows 7 happens to be User/All Users folder which is fine by me.