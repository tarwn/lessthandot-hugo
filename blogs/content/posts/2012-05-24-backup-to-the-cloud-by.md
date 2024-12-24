---
title: Backup to the cloud by using duplicati
author: SQLDenis
type: post
date: 2012-05-24T23:53:00+00:00
ID: 1638
excerpt: 'I decided to give Duplicati a try to backup some of my stuff to the cloud. Duplicati is free/libre/open-source software (FLOSS). Duplicati works with Amazon S3, Windows Live SkyDrive, Google Drive (Google Docs), Rackspace Cloud Files or WebDAV, SSH, FTP&hellip;'
url: /index.php/itprofessionals/softwareandconfigmgmt/backup-to-the-cloud-by/
views:
  - 21244
rp4wp_auto_linked:
  - 1
categories:
  - Book Review
  - Software and Configuration Management
tags:
  - amazon
  - backup
  - cloud
  - open source
  - restore
  - skydrive

---
I decided to give [Duplicati][1] a try to backup some of my stuff to the cloud. Duplicati is free/libre/open-source software (FLOSS). Duplicati works with Amazon S3, Windows Live SkyDrive, Google Drive (Google Docs), Rackspace Cloud Files or WebDAV, SSH, FTP. 

Duplicati uses Pre-Internet Encryption, you know that nobody can decrypt your files. When working with the cloud you have to be in TNO (Trust No One) mode. You never know what malicious person could be on the other side. Duplicati has built-in AES-256 encryption and backups can be signed using GNU Privacy Guard

Duplicati is available for the following platforms

Duplicati 1.3.2 for Windows – (32bit, msi)
  
Duplicati 1.3.2 for Windows – (64bit, msi)
  
Duplicati 1.3.2 for Mac OS X – (dmg)
  
Duplicati 1.3.2 for Debian, Ubuntu, LinuxMint – (deb)
  
Duplicati 1.3.2 for Fedora, RedHat, OpenSuSE – (rpm)
  
Duplicati 1.3.2 – (Binaries, zip)
  
Duplicati 1.3.2 – (Linux binary, tgz)

Let's take a look at how easy it is to use. After you install Duplicati, run it. When you run it the first time a wizard will pop up. Pick select a new backup

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati01.PNG?mtime=1337909180"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati01.PNG?mtime=1337909180" width="506" height="379" /></a>
</div>

A bunch of typical folders that hold your pictures, music, documents etc are shown by default. I decided to create a new folder and dump some pics in that folder

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati02.PNG?mtime=1337909180"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati02.PNG?mtime=1337909180" width="501" height="372" /></a>
</div>

Next you give a password for your backups

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati03.PNG?mtime=1337909181"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati03.PNG?mtime=1337909181" width="506" height="370" /></a>
</div>

Then you pick where you want to back this up to

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati04.PNG?mtime=1337909182"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati04.PNG?mtime=1337909182" width="511" height="340" /></a>
</div>

I picked SkyDrive

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati05.PNG?mtime=1337909183"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati05.PNG?mtime=1337909183" width="506" height="372" /></a>
</div>

On the advanced page, you can add custom filters, set up the frequency and add limits

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati06.PNG?mtime=1337909235"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati06.PNG?mtime=1337909235" width="510" height="376" /></a>
</div>

On the next page you can now fill the details in for the stuff you picked in the previous page

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati07.PNG?mtime=1337909236"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati07.PNG?mtime=1337909236" width="505" height="377" /></a>
</div>

Backup is running....and it is done

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati08.PNG?mtime=1337909236"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati08.PNG?mtime=1337909236" width="472" height="412" /></a>
</div>

I checked that there was something in the SkyDrive folder

Next I wiped out the folder on my PC and decided to do a restore. I ran the app, clicked on the wizard and picked the restore option

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati09.PNG?mtime=1337909237"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati09.PNG?mtime=1337909237" width="505" height="376" /></a>
</div>

I picked restore from an existing backup

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati10.PNG?mtime=1337909238"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati10.PNG?mtime=1337909238" width="504" height="376" /></a>
</div>

I picked the backup I wanted to restore

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati11.PNG?mtime=1337909271"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati11.PNG?mtime=1337909271" width="505" height="378" /></a>
</div>

It is downloading the backup

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati12.PNG?mtime=1337909271"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Duplicati/Duplicati12.PNG?mtime=1337909271" width="507" height="377" /></a>
</div>

I checked the folder and all the pictures that I wiped out were restored

It is pretty easy to use, I will test this out over the coming weeks to get more familiar with this product

Duplicati is an open source project, licensed under LGPL, the source can be downloaded here http://code.google.com/p/duplicati/source/checkout

Try it out and let me know what you think, the main product page is here: http://www.duplicati.com

 [1]: http://www.duplicati.com