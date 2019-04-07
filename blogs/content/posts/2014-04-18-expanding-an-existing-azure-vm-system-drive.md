---
title: Expanding an Existing Azure VM System Drive
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-04-18T18:37:18+00:00
ID: 2464
url: /index.php/enterprisedev/cloud/azure/expanding-an-existing-azure-vm-system-drive/
views:
  - 16180
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - azure

---
I have an Azure VM that was created back when the C: drives had an 30GB size limitation (which leaves maybe 8GB after all the OS files, Windows updates, etc). This present a considerable challenge when I go to install something that has to be installed on the C: Drive, like Visual Studio 2013.

Finding a path from a 30GB primary drive to (anything larger) was a little rough, most of the information I found said you had delete the VM, download the drive, attach it to another VM as a secondary drive, extend it, then re-upload it and do some extra magic to make it usable as a primary drive again and then re-create the VM. Doing all of this from another VM inside Azure looked like it would speed things up a bit, but it was still a pretty painful process.

While I was trying the longer process though, I found a series of smaller tools that people had used to make individual steps easier. I was able to piece all of those together into a process that allowed me to extend the primary drive in place in less time than it took to delete and download the drive to another VM.

## The Quicker Process

So, with a big fat &#8220;this worked on my machine and might blow up yours&#8221; disclaimer, here&#8217;s that process:

1. Stop the server
2. Delete the server (but keep the drives) 
* Azure Dashboard &#8211; Virtual Machines
* Select the Server
* Press Delete
* Select Delete but keep the drives option
3. Backup the drive 
* Get azure-cli
* use the following command to copy the existing VHD into a new location super-fast: 

`azure vm disk upload <source-path> <target-blob-url> <target-storage-account-key>`
_Reference: <http://michaelwasham.com/2012/08/07/copying-vhds-and-other-blobs-between-storage-accounts/>_ 

* Delete the disk 
    * Azure Dashboard &#8211; Virtual Machines
    * Select Disks at top
    * Select the drive + press Delete
    * Select the &#8220;Delete and retain associated VHD&#8221; option
* Resize the Disk 
    * Download WindowsAzureDiskResizer:

      <http://blog.maartenballiauw.be/post/2013/01/07/Tales-from-the-trenches-resizing-a-Windows-Azure-virtual-disk-the-smooth-way.aspx>
    * Use the WindowsAzureDiskResizer with a command like: 

```
E:\Downloads\WindowsAzureDiskResizer-1.0.0.0>WindowsAzureDiskResizer.exe 120 "<URL for blob here>"
```
Output will look like:
          
```
WindowsAzureDiskResizer v1.0.0.0
Copyright 2013 Maarten Balliauw
[4:20 PM] Determining blob size...
[4:20 PM] Reading VHD file format footer...
[4:20 PM] VHD file format fixed, current size 32212254720 bytes.
[4:20 PM] Expanding containing blob...
[4:20 PM] Updating VHD file format footer...
[4:20 PM] New VHD file size 128849018880 bytes, checksum 4294960647.
[4:20 PM] Writing VHD file format footer...
[4:20 PM] Overwriting the old VHD file footer with zeroes...
[4:20 PM] Done!
```

  * Re-Create the Disk 
      * Azure Dashboard &#8211; Virtual Machines &#8211; Disks
      * Select &#8220;Create&#8221; at the bottom
      * Name the drive
      * Select the VHD you just resized
      * Check the &#8220;this contains an OS&#8221; box and select &#8220;Windows&#8221;
  * Re-create the VM 
      * Azure Dashboard
      * New &#8211; Compute &#8211; Virtual Machine &#8211; From Gallery
      * Select &#8220;My Disks&#8221;
      * Select the new drive you just re-created
      * Finish the wizard to setup the VM like it originally was
      * Attach any other drives it originally had (reboot?)
      * Configure ACL rules in the Endpoints of the Virtual Machine
      * RDP Connect to the server
      * you may need to use IP address at this point, my DNS wasn&#8217;t updating and I had used same name as original server
  * Resize the disk 
      * Open Server Manager
      * Select Storage, then Disk Management
      * Select the drive you resized
      * Right click and select &#8220;Expand&#8221;
      * Wizard!
  * Hurrah, the disk is embiggened!
  
  And there you go, all without multi-GB downloads, re-uploads, etc.