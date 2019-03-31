---
title: SSRS Properties – VerticalAlign
author: Jes Borland
type: post
date: 2013-02-18T12:25:00+00:00
excerpt: The purpose of this property is to let you set the vertical text alignment of a text box or tablix cell.
url: /index.php/datamgmt/ssrs/ssrs-properties-verticalalign/
views:
  - 6710
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: VerticalAlign

The purpose of this property is to set the vertical text alignment of a text box or tablix cell.

To access the property, go to the text box and select VerticalAlign. The options are Default, Top, Middle, and Bottom.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/verticalalign 1.png?mtime=1360934468" alt="" width="275" height="123" />
</p>

Options:

<p style="padding-left: 30px;">
  Default – top.
</p>

<p style="padding-left: 30px;">
  Top – text will be placed at the top of the cell and go down.
</p>

<p style="padding-left: 30px;">
  Middle – text will be centered in the middle of the cell.
</p>

<p style="padding-left: 30px;">
  Bottom – text will be placed at the bottom of the cell and go up.
</p>

**Example**: I have a product inventory report. I want to display the text in different vertical alignments. By default, the VerticalAlign is set to Top.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/verticalalign 2.png?mtime=1360934468" alt="" width="584" height="229" />
</p>

I set VerticalAlignfor all text boxes to Middle. The longest lines of text will fill the boxes; other text will be centered vertically.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/verticalalign 3.png?mtime=1360934468" alt="" width="583" height="256" />
</p>

I set VerticalAlign for all text boxes to Bottom. All boxes line the text up at the bottom.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/verticalalign 4.png?mtime=1360934468" alt="" width="577" height="259" />
</p>

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story