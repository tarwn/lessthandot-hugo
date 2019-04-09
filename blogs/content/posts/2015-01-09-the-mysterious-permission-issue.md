---
title: The mysterious permission issue
author: Koen Verbeeck
type: post
date: 2015-01-09T14:58:26+00:00
ID: 3150
url: /index.php/datamgmt/ssis/the-mysterious-permission-issue/
views:
  - 7500
rp4wp_auto_linked:
  - 1
categories:
  - SSIS
tags:
  - 0xC020200E
  - cannot open data file
  - flat file
  - permission issue
  - share
  - ssis
  - syndicated

---
[<img style="float: left;margin: 0px 10px 0px 10px" src="/wp-content/uploads/2015/01/poirot-150x150.jpg" alt="poirot" width="100" height="100" />][1]As you might have realized, this is not the title of an Agatha Christie book, but rather about some nasty permission issue I encountered with SSIS. At a new project, someone mentioned me that a scheduled job has been failing for quite some time now (actually since they created the job, yay for testing!). So I put my detective hat on (with my fake Poirot'stache) and started investigating.

The SSIS catalog report _All Executions_ gave me the following wonderfull error messages:

> Error: FF_DST Write to Flat file failed the pre-execute phase and returned error code 0xC020200E.
  
> DFT Export to Flat File:Error: Cannot open the datafile “\\myServer\c$\rootfolder\myExport\myFlatFile.txt”.

There was also a little warning with a more useful message:

> DFT Export to Flat File:Warning: Access is denied.

I immediately thought: “Haha, no proxy is used in the SQL Server Agent job step!”
  
However, when I checked the job step there was a proxy configured. OK then, they must have forgot to assign permissions to the folder. Alas, permissions were correctly set as well. The plot thickens!

Just to be sure, I gave the user account used by the proxy full permission to the root folder (as someone suggested on a forum). No dice, the job still failed with the exact same error message. I googled around for a bit (like any good detective) for the error code 0xC020200E. Another suggestion I came across was adding the user _Network Service_ to the folder but that was a dead end as well. FYI, the package ran flawlessly in SSDT. Of course it did.

Finally I stumbled upon a MSDN thread where someone suggested it might be an issue with the UNC file path. Apparently sometimes there can be an issue when the drive letter is in the path. In this case: c$. So I created a dedicated share called //myServer/myExport and gave explicit read/write permissions to the proxy account. And lo and behold, the job ran without issues!

The moral of this story: Windows can be a real PITA 🙂

 [1]: /wp-content/uploads/2015/01/poirot.jpg