---
title: Never drag and drop files between servers again!
author: Jes Borland
type: post
date: 2016-02-19T15:22:55+00:00
url: /index.php/uncategorized/never-drag-and-drop-files-between-servers-again/
views:
  - 7093
rp4wp_auto_linked:
  - 1
categories:
  - Management
  - Uncategorized
  - Windows

---
IT professionals (and amateurs), it&#8217;s time we had a chat. It&#8217;s time to stop dragging and dropping (or copying and pasting) files between servers and/or workstations.

It&#8217;s clumsy. It&#8217;s childish. It uses memory on the server.

Oh, and there&#8217;s a really easy tool to copy files built into Windows &#8211; <a href="https://technet.microsoft.com/en-us/library/cc733145.aspx" target="_blank">Robocopy</a>.

Here&#8217;s the basic premise: you open a PowerShell window (or command prompt, but let&#8217;s join the 21st century), and enter

<pre>robocopy &lt;source directory&gt; &lt;destination directory&gt; &lt;optional: file name&gt; /&lt;options&gt;</pre>

The file(s) are copied, with progress shown. A few things I love about this:

  * I can specify a network location or a local directory for either the source or the destination.
  * I can copy one backup file, or every transaction log backup in the directory.
  * I can use the /z option to tell Robocopy that if the transfer gets interrupted, pick back up when it gets reconnected.
  * I can see the progress, in terms of actual bytes, not just Windows&#8217;s &#8220;45%&#8221; that runs for three hours.

Here are a couple examples.

I want to move the files from my Extended Events folder on AG1 to AG 2. Command:

<pre>robocopy "C:\Users\a-jes\Documents\SQL Server Management Studio\Extended Events" "\\SQL2014AG2\C$\Users\a-jes\Documents\SQL Server Management Studio" /z</pre>

<div id="attachment_4389" style="width: 885px" class="wp-caption aligncenter">
  <img class="wp-image-4389 size-full" src="/wp-content/uploads/2016/02/robocopy-1.png" alt="All sorts of goodness!" width="875" height="647" srcset="/wp-content/uploads/2016/02/robocopy-1.png 875w, /wp-content/uploads/2016/02/robocopy-1-300x221.png 300w" sizes="(max-width: 875px) 100vw, 875px" />
  
  <p class="wp-caption-text">
    All sorts of goodness!
  </p>
</div>

&nbsp;

Let&#8217;s say I wanted to move all the .xml.gz files from one directory to another. Command:

<pre>robocopy "C:\Users\a-jes\Documents" "\\SQL2014AG2\C$\Users\a-jes\Documents" *.xml.gz /z </pre>

<img class="aligncenter wp-image-4390 size-full" src="/wp-content/uploads/2016/02/robocopy-2.png" alt="robocopy 2" width="883" height="645" srcset="/wp-content/uploads/2016/02/robocopy-2.png 883w, /wp-content/uploads/2016/02/robocopy-2-300x219.png 300w" sizes="(max-width: 883px) 100vw, 883px" />

That&#8217;s a very quick look at Robocopy. I suggest you know it, use it, and love it!

(Inspired by <a href="https://twitter.com/sqlagentman" target="_blank">Tim Ford</a>&#8216;s <a href="http://thesqlagentman.com/2016/01/entry-level-content/" target="_blank">call to blog about #entrylevel topics</a> that I #wanttoshare.)