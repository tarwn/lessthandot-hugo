---
title: Force installation of 64-bit ACE OLE DB provider
author: Koen Verbeeck
type: post
date: 2013-04-23T06:49:00+00:00
excerpt: This blog post describes how you can install the 64-bit ACE OLE DB provider when you have 32-bit Office components installed.
url: /index.php/datamgmt/dbprogramming/mssqlserver/force-ace-installation/
views:
  - 70742
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - SSIS
tags:
  - 64 bit
  - ace ole db
  - excel
  - run64bitruntime
  - ssis
  - syndicated

---
<p style="text-align: justify;">
  Anyone working with SSIS and Excel probably had the following issue: you are creating an SSIS package using an Excel file in BIDS/SSDT – which is a 32-bit application – and when you try to run the package, it crashes. This is easily fixed by setting the project property <em>Run64BitRuntime</em> to False. The package now runs in 32-bit mode and uses the 32-bit ACE OLE DB provider which was installed alongside Office (if you installed the 32-bit version at least).
</p>

<p style="text-align: justify;">
  If you want to run the package in a SQL Server job, you’d need to set the job step to use the 32-bit version by selecting the checkbox <em>Use 32-bit runtime</em> in the Execution options in SQL Server 2008 or later, or by using the 32-bit version of DTEXEC in the command line arguments in SQL Server 2005. (For an overview, check out this excellent blog post by Todd McDermid (<a href="http://toddmcdermid.blogspot.be/">blog</a> | <a href="https://twitter.com/Todd_McDermid">twitter</a>): <a href="http://toddmcdermid.blogspot.be/2009/10/quick-reference-ssis-in-32-and-64-bits.html">Quick Reference: SSIS in 32- and 64-bits</a>)
</p>

<p style="text-align: justify;">
  But what if you want to run the package in 64-bit mode? Easy, you install the 64-bit ACE OLE DB provider which you can download <a href="http://www.microsoft.com/en-us/download/details.aspx?id=13255">here</a>. Note that there isn’t a 64-bit version available for the 2007 redistributable and that at the time of writing the 2013 version hasn’t been released at all. With the 64-bit provider installed, you can run your packages on a 64-bit server.
</p>

<p style="text-align: justify;">
  If you don’t install the provider, you get the following error when you try to run such a package on a 64-bit SQL Server:
</p>

<p style="text-align: justify;">
  <em>The requested OLE DB provider Microsoft.ACE.OLEDB.12.0 is not registered. If the 64-bit driver is not installed, run the package in 32-bit mode. … Description: “Class not registered”</em>
</p>

<p style="text-align: justify;">
  <em> </em>
</p>

<div class="image_block">
  <em><a href="/media/users/koenverbeeck/ForceACE/ExecutionResult_2.png?mtime=1366689582"><img src="/wp-content/uploads/users/koenverbeeck/ForceACE/ExecutionResult_2.png?mtime=1366689582" alt="" width="857" height="137" /></a></em>
</div>

<span style="text-align: justify;">However, if Office is installed in the 32-bit version, the installation of the 64-bit ACE OLE DB provider fails with the following message:</span>

<p style="text-align: justify;">
  <em>You cannot install the 64-bit version of Microsoft Access Database Engine 2010 because you currently have 32-bit Office products installed.</em>
</p>

<p style="text-align: justify;">
  <em> </em>
</p>

<div class="image_block">
  <em><a href="/media/users/koenverbeeck/ForceACE/Error_64.png?mtime=1366689573"><img src="/wp-content/uploads/users/koenverbeeck/ForceACE/Error_64.png?mtime=1366689573" alt="" width="362" height="190" /></a></em>
</div>

 __

<span style="text-align: justify;">It basically tells us to uninstall Office before you can proceed with the installation. The funny part is that when I try to install the 32-bit version of the ACE OLE DB provider, I’m greeted with the same message:</span>

<p style="text-align: justify;">
   
</p>

<div class="image_block">
  <a href="/media/users/koenverbeeck/ForceACE/Error_32.png?mtime=1366689566"><img src="/wp-content/uploads/users/koenverbeeck/ForceACE/Error_32.png?mtime=1366689566" alt="" width="362" height="190" /></a>
</div>

Apparently a teensy-weensy part of Office was installed in 64-bit, so I can’t install the 32-bit provider either. Normally you wouldn’t have these problems on a production server, as no Office components are installed. But what if you want to test your SQL Server Agent job on your development machine? Or what if you are reading the Excel file using an OPENROWSET command in SSMS? You need that 64-bit provider badly!

Luckily I came across a solution offered by Lowell in this [thread][1]: apparently it works when you install the provider through the command line. All you need to do is add the **/passive** switch and the installation runs without an issue.

Let’s test if this solves our issue. I created a simple SSIS package reading from an Excel file. Nothing too fancy.

<p style="text-align: center;">
  <a href="/media/users/koenverbeeck/ForceACE/BIDS_Setup.png?mtime=1366689558"><img src="/wp-content/uploads/users/koenverbeeck/ForceACE/BIDS_Setup.png?mtime=1366689558" alt="" width="547" height="304" /></a>
</p>

When I deploy it to the SSIS catalog and execute it in 64-bit, I see the package has ran successfully:

<p style="text-align: center;">
  <a href="/media/users/koenverbeeck/ForceACE/ExecutionResult_3.png?mtime=1366689588"><img src="/wp-content/uploads/users/koenverbeeck/ForceACE/ExecutionResult_3.png?mtime=1366689588" alt="" width="307" height="162" /></a>
</p>

What’s interesting is that on my new laptop, which has Office 2013 64-bit installed, I could install the 32-bit provider without an issue. So maybe the Office team fixed the issue in their latest release.

**Conclusion**

This blog post shows a very simple solution on how to solve a very common problem with SSIS and Excel. I’d like to thank Lowell for pointing me out to this solution in the thread I mentioned earlier.

_Update: Installing both the providers on the same machine with Office components installed doesn&#8217;t mean your life is issue-free from now on. Apparently this set-up can cause issues in other places. Check out the comments for more details._

 [1]: http://www.sqlservercentral.com/Forums/Topic1407044-391-1.aspx