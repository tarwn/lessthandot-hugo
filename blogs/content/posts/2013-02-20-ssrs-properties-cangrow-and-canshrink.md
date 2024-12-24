---
title: SSRS Properties – CanGrow and CanShrink
author: Jes Borland
type: post
date: 2013-02-20T15:09:00+00:00
ID: 1999
excerpt: The purpose of these properties is to allow a text box to grow or reduce in size to fit a value.
url: /index.php/datamgmt/ssrs/ssrs-properties-cangrow-and-canshrink/
views:
  - 10960
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: CanGrow and CanShrink

The purpose of these properties is to allow a text box to grow or reduce in size to fit a value.

To access the property, select a text box and go to CanGrow or CanShrink. The options for both are True or False.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/cangrow 1.png?mtime=1360935264" alt="" width="271" height="81" />
</p>

CanGrow

<p style="padding-left: 30px;">
  True – the text box will grow in size to fit a large value (default).
</p>

<p style="padding-left: 30px;">
  False – the text box will not grow.
</p>

CanShrink

<p style="padding-left: 30px;">
  True – the text box will reduce in size to fit a small value.
</p>

<p style="padding-left: 30px;">
  False – the text box will not reduce (default).
</p>

**Example**: I have an order status report that includes a text box for comments. CanGrow is false, and CanShrink is false.

Showing an order that has no comment, the text box will remain its default size.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/cangrow 2.png?mtime=1360935264" alt="" width="412" height="240" />
</p>

I have no order with a short comment. The text box remains the default size.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/cangrow 3.png?mtime=1360935264" alt="" width="426" height="216" />
</p>

I set CanShrink to True. The text box reduces in size to fit the shorter comment.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/cangrow 4.png?mtime=1360935264" alt="" width="410" height="182" />
</p>

I have another order with a longer comment. I set CanGrow to True. The text box enlarges in size to fix the longer comment.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/cangrow 5.png?mtime=1360935264" alt="" width="407" height="226" />
</p>

**Further Reading:** 

[Allow a Text Box to Grow or Shrink (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://technet.microsoft.com/en-us/library/ff519561.aspx