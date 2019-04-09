---
title: Putting files into year folders using powershell
author: Christiaan Baes (chrissie1)
type: post
date: 2016-02-10T09:38:09+00:00
ID: 4349
url: /index.php/uncategorized/putting-files-into-year-folders-using-powershell/
views:
  - 1685
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized
tags:
  - powershell

---
Powershell is great (as am I) for automating boring little tasks. 

So the question came to put 18 thousand plus image files that were in one folder into folders per year of Date created. 

So to no longer keep you in suspense, here is the complete code<pre lang=powershell>$SourceDir = "" $DestinationDir = "" Write-Host "Source directory: " + $SourceDir Write-Host "Destination directory:" + $DestinationDir $files = get-childitem $SourceDir \*.\* -File |Select-Object -last 10 Write-Host "Number of files to move:" $files.Count foreach($file in $files) { $Directory = $DestinationDir + "\" + $file.LastWriteTime.Date.ToString("yyyy") if(!(Test-Path $Directory)) { Write-Host "Creating directory" New-Item $Directory -type directory } Write-Host "To directory:" $Directory Write-Host "Moving file:" $file.ToString() Move-Item $file.fullname $Directory Write-Host "File moved" } </pre> 

This only takes the last 10 files and it uses the LastWriteTime but you get the drift.

Just fill in the Source and Destination Directories and you're good to go.

Most important parts.<pre lang=powershell>$files = get-childitem $SourceDir \*.\* -File |Select-Object -last 10</pre> 

Gets the files form the sourcedirectory and selects the last 10<pre lang=powershell>$Directory = $DestinationDir + "\" + $file.LastWriteTime.Date.ToString("yyyy") if(!(Test-Path $Directory)) { Write-Host "Creating directory" New-Item $Directory -type directory }</pre> 

Makes a new directory per year if it not already exists.<pre lang=powershell>Move-Item $file.fullname $Directory</pre> 

Moves the file.

Simple and quick. All 18k files moved in less than a minute.