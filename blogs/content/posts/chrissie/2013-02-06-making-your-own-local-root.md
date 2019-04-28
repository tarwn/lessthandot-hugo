---
title: Making your own Local root CA and using it as an SSL certificate for intranet websites.
author: Christiaan Baes (chrissie1)
type: post
date: 2013-02-06T11:11:00+00:00
ID: 1972
excerpt: Setting up SSL for your intranetsite using the Active directory certificate services. Because self-signing is no good.
url: /index.php/sysadmins/os/windows/making-your-own-local-root/
views:
  - 10492
categories:
  - 2008 Server
  - Windows
tags:
  - certificate
  - iis
  - ssl

---
## Introduction

I had my intranet site selfsigned before. But the problem with self signing is that the user gets a warning.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate1.png?mtime=1360153997"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate1.png?mtime=1360153997" width="714" height="344" /></a>
</div>

And of course we all click continue and then you get this.
  
Which makes the addressbar totally useless because gray on pink is not very readable.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate2.png?mtime=1360154006"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate2.png?mtime=1360154006" width="587" height="47" /></a>
</div>

So I set out to fix it. And here is my story.

## Installation

You should install the Active directory certificate services. Go to [technet to learn][1] how to do that for your server, I did.
  
This should apparently be installed on your domain controller. And yes I tried on a member server and no that was no success, so just listen to me.

## IIS

Now go to you IIS manager and click on your server and find the option Server certificates.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate3.png?mtime=1360154614"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate3.png?mtime=1360154614" width="599" height="238" /></a>
</div>

Then in the Actions menu select Create Domain certificate.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate4.png?mtime=1360154727"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate4.png?mtime=1360154727" width="234" height="233" /></a>
</div>

You will then see this.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate5.png?mtime=1360154849"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate5.png?mtime=1360154849" width="580" height="444" /></a>
</div>

Be careful to fill in the correct common name, the common name should be the domain name of your website. The part that comes after the https:// part and before the next /.

Then next.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate6.png?mtime=1360154965"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate6.png?mtime=1360154965" width="581" height="445" /></a>
</div>

Then pick your authority service, if all went well that should be the one you installed on your domain controller.
  
And give it a friendly name.

Now go to your site and click on Bindings in the Actions thing.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate7.png?mtime=1360155190"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate7.png?mtime=1360155190" width="226" height="193" /></a>
</div>

Select https as the type and set the port and select your certificate by its friendly name.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate8.png?mtime=1360155319"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate8.png?mtime=1360155319" width="490" height="226" /></a>
</div>

## The clients

Now it&#8217;s time to configure our clients. 

Since we are on a domain controller we can use a group policy for that.

First we need to get the certificate.

Now open MMC on your webserver and add certificates for your computer account on your local machine. 

Then go to personal and certificates. Right-click on your certificate and select export.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate9.png?mtime=1360155468"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate9.png?mtime=1360155468" width="825" height="215" /></a>
</div>

Remeber where you parked it and copy it to your domain controller.

Now open the Group policy manager.

Click on your Default Domain Policy.

Go to Computer configuration -> Policies -> Windows settings -> Security settings -> Public key policies -> Trusted Root Certification Authorities -> Take a deep breath.

Now right click that and select import.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate10.png?mtime=1360156026"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate10.png?mtime=1360156026" width="396" height="412" /></a>
</div>

Select your certificate that you exported a while ago and wait for the clients to replicate.

Done.

Now you get this.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/SysAdmins/certificate/certificate11.png?mtime=1360156204"><img alt="" src="/wp-content/uploads/blogs/SysAdmins/certificate/certificate11.png?mtime=1360156204" width="573" height="246" /></a>
</div>

No, it&#8217;s not fully trusted but it no longer gives a warning either.

And Chris is happy.

## Conclusion

This is here for my own reference.

 [1]: http://technet.microsoft.com/en-us/library/cc947821(v=ws.10).aspx