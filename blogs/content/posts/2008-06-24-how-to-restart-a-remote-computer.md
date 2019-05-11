---
title: How To Restart A Remote Computer
author: SQLDenis
type: post
date: 2008-06-24T16:55:02+00:00
ID: 73
url: /index.php/sysadmins/os/how-to-restart-a-remote-computer/
views:
  - 7756
rp4wp_auto_linked:
  - 1
categories:
  - Operating Systems

---
Sometimes you have to login to your work PC from home over the VPN and after a while for some reason or another you want to restart your PC. How can you do that? You can't use the start menu because only the log off button is displayed
  
Well one way is to open a command window and executing **shutdown -r** 
  
That will restart your computer 

Here is the basic usage of the shutdown command 

Usage: shutdown \[-i -l -s -r -a\] \[-f\] \[-m \computername\] \[-t xx\] \[-c "comment"\] \[-d up:xx:yy\] 

No args Display this message (same as -?)
  
-i Display GUI interface, must be the first option
  
-l Log off (cannot be used with -m option)
  
-s Shutdown the computer
  
-r Shutdown and restart the computer
  
-a Abort a system shutdown
  
-m \computername Remote computer to shutdown/restart/abort
  
-t xx Set timeout for shutdown to xx seconds
  
-c "comment" Shutdown comment (maximum of 127 characters)
  
-f Forces running applications to close without warning
  
-d \[u\]\[p\]:xx:yy The reason code for the shutdown
  
u is the user code
  
p is a planned shutdown code
  
xx is the major reason code (positive integer less than 256)
  
yy is the minor reason code (positive integer less than 65536)