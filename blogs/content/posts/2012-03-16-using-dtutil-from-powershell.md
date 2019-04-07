---
title: Using DTUtil from PowerShell
author: Axel Achten (axel8s)
type: post
date: 2012-03-16T09:43:00+00:00
excerpt: |
  I'm preparing my demo's for a MS6235A SSIS training I will be teaching in a couple of days. In one of the demo's I just copy a SSIS package from the file system to SQL Server using the DTUtil from the command prompt with this command:
  DTUTIL /FILE <&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/using-dtutil-from-powershell/
views:
  - 8747
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
  - SSIS
tags:
  - dtutil
  - posh
  - powershell
  - ssis

---
I&#8217;m preparing my demo&#8217;s for a [MS6235A SSIS training][1] I will be teaching in a couple of days. In one of the demo&#8217;s I just copy a SSIS package from the file system to SQL Server using the DTUtil from the command prompt with this command:

<pre>DTUTIL /FILE <Path to package&gt;.dtsx /COPY SQL;<Packagename&gt;</pre>

As expected this works like a charm:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DTUtilPoSH1.png?mtime=1331897781"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DTUtilPoSH1.png?mtime=1331897781" width="678" height="134" /></a>
</div>

But on my demo machine I had a PowerShell window open and started from there. I first tried if the DTUtil command is recognized:

<pre>DTUtil /?</pre>

And seeing the output it is:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DTUtilPoSH2.png?mtime=1331897793"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DTUtilPoSH2.png?mtime=1331897793" width="997" height="337" /></a>
</div>

So I used the same command as in the CMD-prompt to copy the package:

<pre>DTUTIL /FILE <Path to package&gt;.dtsx /COPY SQL;<Packagename&gt;</pre>

But now I got the error message that &#8220;Packagename&#8221; was not recognized as the name of a cmdlet, function, script file, or operable program.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DTUtilPoSH3.png?mtime=1331897806"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DTUtilPoSH3.png?mtime=1331897806" width="997" height="185" /></a>
</div>

Looking at the code I realized that there was a semicolon right before the &#8220;Packagename&#8221; and apparently, just like in T-SQL, the semicolon is a statement seperator in PowerShell.
  
So when I put the DTUtil parameters between double quotes it should work:

<pre>DTUTIL "/FILE <Path to package&gt;.dtsx /COPY SQL;<Packagename&gt;"</pre>

And it does:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DTUtilPoSH4.png?mtime=1331897833"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DTUtilPoSH4.png?mtime=1331897833" width="884" height="106" /></a>
</div>

The conclusion is that it&#8217;s perfectly possible to use DTUtil from PowerShell and it&#8217;s very interesting to learn this new technology by using it for known tasks but keep in mind what Ted said in his post [Forgotten art of using what you know][2]. I didn&#8217;t use the formula but writing the DTUtil command from the CMD-line took me about a minute. Writing it in PowerShell made me &#8220;lose&#8221; more than an hour (including writing this post).

 [1]: http://www.microsoft.com/learning/en/us/course.aspx?ID=6235A&locale=en-us
 [2]: /index.php/ITProfessionals/ProfessionalDevelopment/forgotten-art-of-using-what