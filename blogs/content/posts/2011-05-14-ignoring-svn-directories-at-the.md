---
title: Ignoring SVN Directories at the Command Line
author: Rob Earl
type: post
date: 2011-05-14T13:20:00+00:00
excerpt: |
  A colleague of mine told me this neat trick which I had to share:
  
  echo "FIGNORE=.svn" &gt;&gt; ~/.bashrc
  
  Now when you're moving around subversion controlled directories with only one subdirectory, hitting tab won't try to autocomplete to ".svn/".
url: /index.php/desktopdev/generalpurposelanguages/ignoring-svn-directories-at-the/
views:
  - 3672
rp4wp_auto_linked:
  - 1
categories:
  - General Purpose Languages
tags:
  - bash
  - cli
  - linux
  - subversion
  - svn

---
A colleague of mine told me this neat trick which I had to share:

<pre>echo "FIGNORE=.svn" &gt;&gt; ~/.bashrc
source ~/.bashrc</pre>

Now when you&#8217;re moving around subversion controlled directories with only one subdirectory, hitting tab won&#8217;t try to autocomplete to &#8220;.svn/&#8221;.