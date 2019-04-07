---
title: Unexpected result with the File System Task
author: Koen Verbeeck
type: post
date: 2013-10-09T12:22:00+00:00
ID: 2173
excerpt: 'Recently I ran across a forum thread where someone encountered an unexpected result when creating a directory using the File System Task in Integration Services. He used a variable to specify the name of the directory to be created and this variable was&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/unexpected-result-with-the-file/
views:
  - 30146
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - SSIS
tags:
  - configurations
  - create directory
  - file system task
  - ssis
  - syndicated

---
<p style="text-align: justify;">
  Recently I ran across a forum thread where someone encountered an unexpected result when creating a directory using the File System Task in Integration Services. He used a variable to specify the name of the directory to be created and this variable was populated by a parent package configuration. If the configuration failed, he didn’t want the package to create the directory specified during design time, so he entered “invalid” in the variable value, expecting the package to fail as it is not a correct path to a directory. This is a common practice to make sure the package doesn’t change your development environment if the configuration in production fails. If you get for example an error from a flat file source saying: “cannot find file invalid”, you immediately know the problem lies with the package configurations.
</p>

<p style="text-align: justify;">
  However, the package did not fail. Instead, it created a directory called <em>Invalid</em>. I recreated the set-up using this simple package:
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/FileSystemTask/packagesetup.PNG?mtime=1381321257"><img src="/wp-content/uploads/users/koenverbeeck/FileSystemTask/packagesetup.PNG?mtime=1381321257" alt="" width="680" height="228" /></a>
</p>

<span style="text-align: justify;">When I run the package using SSIS 2012 in Windows 8, the folder </span>_HelloWorld!!!_ <span style="text-align: justify;">Is created in the same folder where the SSIS package is stored. On another computer – using SSIS 2008R2 in Windows XP – the folder is created in the My Documents folder. The guy from the forum thread mentioned his folder was created on his desktop. The documentation doesn’t mention where a directory is created when there is no absolute path specified and a quick Internet search didn’t yield an explanation either. It’s also unclear if the choice where the directory is finally created is influenced by the version of SSIS, the OS version or some obscure setting no one knows about.</span>

<p style="text-align: justify;">
  Conclusion: very unpredictable results. The directory might still be created and who knows where all your files end up. Better avoid this way of working with a file system task. If you really want to validate directory paths and handle exceptions, I recommend a script task.
</p>