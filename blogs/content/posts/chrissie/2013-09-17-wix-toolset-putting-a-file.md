---
title: WIX toolset – Putting a file in a folder in your installfolder
author: Christiaan Baes (chrissie1)
type: post
date: 2013-09-17T07:13:00+00:00
ID: 2159
excerpt: How to put a file in a folder in your installfolder.
url: /index.php/desktopdev/mstech/wix-toolset-putting-a-file/
views:
  - 12113
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - folder
  - wix toolset

---
[Wix][1] is lots of xml but for simple setup programs it isn&#8217;t all that bad. I guess it can become a maintenance problem for big projects but for small things it&#8217;s kind of easy to use, once you understand the madness and forget the bad documentation.

So I like to keep some order in my installfolder (the one where your program is installed. 

I like to keep the helpfile(s) in their separate little folder. And my program also goes to look for them in that folder. So we need to recreate this.

Typically your Wix would look something like this.

```xml
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;Wix xmlns="http://schemas.microsoft.com/wix/2006/wi"&gt;
	&lt;Product Id="*" Name="test" Language="1033" Version="1.0.0.0" Manufacturer="Me" UpgradeCode="someguid"&gt;
		&lt;Package InstallerVersion="200" Compressed="yes" InstallScope="perMachine" /&gt;

    &lt;Feature Id="ProductFeature" Title="Test" Level="1"&gt;
      &lt;ComponentGroupRef Id="TestComponents" /&gt;
    &lt;/Feature&gt;
  
		&lt;MajorUpgrade DowngradeErrorMessage="A newer version of [ProductName] is already installed." /&gt;
		&lt;MediaTemplate /&gt;
		
	&lt;/Product&gt;
 &lt;Fragment&gt;
    &lt;Directory Id="TARGETDIR" Name="SourceDir"&gt;
      &lt;Directory Id="ProgramFilesFolder"&gt;
        &lt;Directory Id="INSTALLFOLDER" Name="Test"&gt;          
        &lt;/Directory&gt;
      &lt;/Directory&gt;
      &lt;Directory Id="ProgramMenuFolder"&gt;
          &lt;Directory Id="ApplicationProgramsFolder" Name="Test"/&gt;
      &lt;/Directory&gt;
    &lt;/Directory&gt;
  &lt;/Fragment&gt;

  &lt;Fragment&gt;
    &lt;ComponentGroup Id="TestComponents" Directory="INSTALLFOLDER"&gt;
      &lt;Component Id="Test.exe" Guid="someguid"&gt;
        &lt;File Id="test.exe" Source="$(var.Test.TargetPath)" KeyPath="yes" Checksum="yes"/&gt;
      &lt;/Component&gt;
      &lt;Component Id="NLog.dll" Guid="someguid"&gt;
        &lt;File Id="NLog.dll" Source="$(var.Test.TargetDir)NLog.dll" KeyPath="yes"/&gt;
      &lt;/Component&gt;
      &lt;Component Id="testhelp.chm" Guid="someguid"&gt;
        &lt;File Id="testhelp.chm" Source="$(var.Test.TargetDir)helpfilestesthelp.chm" KeyPath="yes"/&gt;
      &lt;/Component&gt;
  &lt;/Fragment&gt;
&lt;/Wix&gt;```
But the above will put your helpfile straight into root of the installfolder. Of course now my program is looking for it in the helpfiles folder instead of the root.

Not to worry we can simply fix this.

```xml
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;Wix xmlns="http://schemas.microsoft.com/wix/2006/wi"&gt;
	&lt;Product Id="*" Name="test" Language="1033" Version="1.0.0.0" Manufacturer="Me" UpgradeCode="someguid"&gt;
		&lt;Package InstallerVersion="200" Compressed="yes" InstallScope="perMachine" /&gt;

    &lt;Feature Id="ProductFeature" Title="Test" Level="1"&gt;
      &lt;ComponentGroupRef Id="TestComponents" /&gt;
      &lt;ComponentGroupRef Id="HelpFilesComponents" /&gt;
    &lt;/Feature&gt;
  
		&lt;MajorUpgrade DowngradeErrorMessage="A newer version of [ProductName] is already installed." /&gt;
		&lt;MediaTemplate /&gt;
		
	&lt;/Product&gt;
 
&lt;Fragment&gt;
    &lt;Directory Id="TARGETDIR" Name="SourceDir"&gt;
      &lt;Directory Id="ProgramFilesFolder"&gt;
        &lt;Directory Id="INSTALLFOLDER" Name="Test"&gt;
            &lt;Directory Id="HELPFILES" Name="HelpFiles" /&gt;
        &lt;/Directory&gt;
      &lt;/Directory&gt;
      &lt;Directory Id="ProgramMenuFolder"&gt;
          &lt;Directory Id="ApplicationProgramsFolder" Name="Test"/&gt;
      &lt;/Directory&gt;
    &lt;/Directory&gt;
  &lt;/Fragment&gt;

  &lt;Fragment&gt;
    &lt;ComponentGroup Id="TestComponents" Directory="INSTALLFOLDER"&gt;
      &lt;Component Id="Test.exe" Guid="someguid"&gt;
        &lt;File Id="test.exe" Source="$(var.Test.TargetPath)" KeyPath="yes" Checksum="yes"/&gt;
      &lt;/Component&gt;
      &lt;Component Id="NLog.dll" Guid="someguid"&gt;
        &lt;File Id="NLog.dll" Source="$(var.Test.TargetDir)NLog.dll" KeyPath="yes"/&gt;
      &lt;/Component&gt;      
    &lt;/ComponentGroup&gt;
    &lt;ComponentGroup Id="HelpFilesComponents" Directory="HELPFILES"&gt;
      &lt;Component Id="testhelp.chm" Guid="someguid"&gt;
        &lt;File Id="testhelp.chm" Source="$(var.Test.TargetDir)helpfilestesthelp.chm" KeyPath="yes"/&gt;
      &lt;/Component&gt;
    &lt;/ComponentGroup&gt;
  &lt;/Fragment&gt;
&lt;/Wix&gt;```
So we add a ComponentGroupRef to our feature tag in the product tags and give it an id of HelpFilesComponents. 

We add a directory tag to the installfolder directory. That way it becomes a subfolder of the installfolder.

And we add a Componentgroup with a helpfilescomponents id and a directory using the Id of helpfiles and move the chm file component to that group.

And now your helpfile is in the correct folder as it should be.

 [1]: http://wixtoolset.org/