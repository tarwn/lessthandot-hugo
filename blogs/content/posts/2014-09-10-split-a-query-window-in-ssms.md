---
title: Split a Query Window in SSMS
author: Jes Borland
type: post
date: 2014-09-10T19:23:04+00:00
url: /index.php/datamgmt/dbprogramming/split-a-query-window-in-ssms/
views:
  - 7053
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
SQL Server Management Studio (SSMS) has <a href="/index.php/datamgmt/dbprogramming/tips-and-tricks-to-make/" target="_blank">a lot of cool little tips and tricks</a>. Last night, while presenting for 24 Hours of PASS, I used the Window Split function and noticed a few &#8220;oohs&#8221; and &#8220;aaahs&#8221; on Twitter.

Here&#8217;s how you do it.

Open a query in SSMS. In this case, I was looking at a nonclustered index creation statement, and wanted to keep it at the top of the window while I ran several queries, seeing if they would use it.

[<img class="alignleft wp-image-2933 size-full" src="/wp-content/uploads/2014/09/window-split-1.png" alt="window split 1" width="1294" height="842" srcset="/wp-content/uploads/2014/09/window-split-1.png 1294w, /wp-content/uploads/2014/09/window-split-1-300x195.png 300w, /wp-content/uploads/2014/09/window-split-1-1024x666.png 1024w" sizes="(max-width: 1294px) 100vw, 1294px" />][1]

To split the upper half of the window, where my query is, I can either go to Window > Split, or click the little (and I mean teeny-tiny) double-headed arrow in the top right.

[<img class="aligncenter size-full wp-image-2938" src="/wp-content/uploads/2014/09/window-split-2.png" alt="window split 2" width="982" height="231" srcset="/wp-content/uploads/2014/09/window-split-2.png 982w, /wp-content/uploads/2014/09/window-split-2-300x70.png 300w" sizes="(max-width: 982px) 100vw, 982px" />][2]Now, I can view two different sections of the query on the same screen.

[<img class="aligncenter size-full wp-image-2939" src="/wp-content/uploads/2014/09/window-split-3.png" alt="window split 3" width="963" height="704" srcset="/wp-content/uploads/2014/09/window-split-3.png 963w, /wp-content/uploads/2014/09/window-split-3-300x219.png 300w" sizes="(max-width: 963px) 100vw, 963px" />][3]

&nbsp;

This tip can be helpful for many situations. Think of viewing a stored procedure or function, and keeping the list of parameters at the top of the window. It can be really helpful when combined with the Tools > Options> Query Results > SQL Server > Results to Grid > &#8220;Display results in a separate tab&#8221; option, for keeping large portions of the query on the screen at one time.

&nbsp;

&nbsp;

 [1]: /wp-content/uploads/2014/09/window-split-1.png
 [2]: /wp-content/uploads/2014/09/window-split-2.png
 [3]: /wp-content/uploads/2014/09/window-split-3.png