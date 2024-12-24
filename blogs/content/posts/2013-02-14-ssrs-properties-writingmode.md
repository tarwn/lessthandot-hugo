---
title: SSRS Properties – WritingMode
author: Jes Borland
type: post
date: 2013-02-14T12:11:00+00:00
ID: 1993
excerpt: The purpose of this property is to allow you to display text horizontally or vertically within a text box or tablix cell.
url: /index.php/datamgmt/ssrs/ssrs-properties-writingmode/
views:
  - 8551
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: WritingMode

The purpose of this property is to allow you to display text horizontally or vertically within a text box or tablix cell.

To access the property, select the text box and go to WritingMode. The options are Horizontal, Vertical, and Rotate270.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/writingmode 1.png?mtime=1360850970" alt="" width="236" height="138" />
</p>

Modes

<p style="padding-left: 30px;">
  Horizontal: Text will be horizontal, read left to right.
</p>

<p style="padding-left: 30px;">
  Vertical: Text will be vertical, read top to bottom.
</p>

<p style="padding-left: 30px;">
  Rotate 270: Text will be vertical, read bottom to top.
</p>

**Example**: I have a product inventory report with several headers. By default, the text displays horizontally.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/writingmode 2.png?mtime=1360850970" alt="" width="690" height="212" />
</p>

I change the WritingMode of the column headers to Vertical. The text is read from top to bottom, starting in the top right corner of the cell.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/writingmode 3.png?mtime=1360850970" alt="" width="691" height="241" />
</p>

I change the WritingMode of the column headers to Rotate270. The text is read from bottom to top, starting in the lower left corner of the cell.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/writingmode 4.png?mtime=1360850970" alt="" width="700" height="252" />
</p>

**Further Reading:** 

[Set Text Box Orientation (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://technet.microsoft.com/en-us/library/ee633659.aspx