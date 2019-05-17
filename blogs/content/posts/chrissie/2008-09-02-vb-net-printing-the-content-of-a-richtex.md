---
title: 'VB.Net: Printing the content of a RichtextBox'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-02T08:02:59+00:00
ID: 121
url: /index.php/desktopdev/mstech/vb-net-printing-the-content-of-a-richtex/
views:
  - 15429
categories:
  - Microsoft Technologies
tags:
  - print
  - richtextbox
  - vb.net

---
Today, I was looking for a method to Print the text that is in a RichTextbox. The printing of the text is no problem but keeping the formatting is a little more troublesome. 

The first solutions you find use VB6 to accomplish this (by using an ActiveX control. But that was just not happening in my project. 

Then, [helped by an article on MSDN][1], I found [the solution][2], which is actually just a com call and the same as what the others are doing. The code is in our [wiki][2] because I don&#8217;t want to bore you guys with all the details. 

I used that to make an extension method for the richtextbox, which makes printing the contents of it a lot easier. Just do this

```vbnet
RichTextBox1.Print()```
or 

```vbnet
RichTextBox1.PrintPreview()```
I also made it so that you can add a simple Header and Footer. Of course, you could make simple variations on them.

Here is an example of it at work in my home-made queryanalyser. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/queryanalyser1.jpg" alt="" title="" width="482" height="542" />
</div>

<div class="image_block">
  <p>
    And then with this simple little code:
  </p>
  
  <pre lang="vbnet">CType(Me.TabControl1.SelectedTab.Controls(0), System.Windows.Forms.RichTextBox).PrintPreview(Me.TabControl1.SelectedTab.Text, "Printed On: " & Now.ToString("dd/MM/yyyy HH:mm"))</pre>
  
  <p>
    It gives this as a result:
  </p>
  
  <p>
    <img src="/wp-content/uploads/blogs/DesktopDev/queryanalyserprintpreview.jpg" alt="" title="" width="428" height="331" /></div> 
    
    <p>
      Don&#8217;t forget, you can always ask questions about this in our <a href="http://forum.lessthandot.com/viewforum.php?f=39">VB.Net Forum</a>
    </p>

 [1]: http://msdn.microsoft.com/en-us/library/ms996492.aspx
 [2]: http://wiki.lessthandot.com/index.php/VB.Net:_Extension_method_Print_RichTextBox