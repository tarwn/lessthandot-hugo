---
title: 'VB.Net: Print a DataGridView with RustemSoft’s Print Class and an Extension method'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-02T11:45:39+00:00
ID: 122
url: /index.php/desktopdev/mstech/vb-net-print-a-datagridview-with-rustems/
views:
  - 5745
categories:
  - Microsoft Technologies
tags:
  - datagridview
  - rustemsoft
  - vb.net

---
Today I also needed to print the datagridview that was filled with the resutls of my queries. Instead of using a report engine, I used a tool already in my toolbox, namely Rustemsoft&#8217;s datagridviewprint class. I made another extension method (I really like those things). [You can find the code on our wiki][1].

And here is an example. I obfuscated the results just so it looks a bit more important ;-).

This is the datagridview.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/QueryanalyserDatagridview.jpg" alt="" title="" width="531" height="540" />
</div>

And this is the printpreview.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/QueryanalyserDatagridviewpreview.jpg" alt="" title="" width="316" height="416" />
</div>

And all that by just having this as the code.

```vbnet
Me.DataGridView1.printPreview(Me.TabControl1.SelectedTab.Text, "Printed On: " & Now.ToString("dd/MM/yyyy HH:mm"))```
I do have to say that this method is a bit slow but I am using an older version, perhaps the new one is faster.

You can always ask question about [VB.Net on the forums][2].

 [1]: http://wiki.lessthandot.com/index.php/VB.Net:_Print_a_DataGridView_with_RustemSoft_%27s_Print_Class_and_an_Extension_method
 [2]: http://forum.lessthandot.com