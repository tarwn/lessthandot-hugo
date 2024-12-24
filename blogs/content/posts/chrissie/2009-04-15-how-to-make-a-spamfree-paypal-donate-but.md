---
title: How to make a spamfree paypal donate button
author: Christiaan Baes (chrissie1)
type: post
date: 2009-04-15T18:01:48+00:00
ID: 390
url: /index.php/webdev/webdesigngraphicsstyling/how-to-make-a-spamfree-paypal-donate-but/
views:
  - 7460
categories:
  - Web Design, Graphics and Styling
tags:
  - donate
  - html
  - lessthandot
  - paypal

---
Thz following piece of code demonstrates the normal way of making a donate button that uses your paypal account.

```html
&lt;form name="_xclick" action="https://www.paypal.com/cgi-bin/webscr" method="post"&gt;
&lt;input type="hidden" name="cmd" value="_xclick"&gt;
&lt;input type="hidden" name="business" value="email@you.us"&gt;
&lt;input type="hidden" name="item_name" value="yourvalue"&gt;
&lt;input type="hidden" name="currency_code" value="USD"&gt;
&lt;input type="hidden" name="amount" value="100.00"&gt;
&lt;input type="submit" value="$100.00" name="submit100" alt="Make payments with PayPal - it's fast, free and secure!"&gt;
&lt;/form&gt;```
What is wrong with this? Well, it has an email address that is associated with your paypal account in there in plain text and spambots are still active and will harvest it sadly.

So you have to find a better way to do this and if you have a bussines account you can (not sure about normal accounts). 

So here is how to make them. Login to paypal and click on the merchant services tab. there it has a create buttons category and a donate link. That takes you here.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/WebDev/paypal.jpg" alt="" title="" width="751" height="634" />
</div>

Just make sure you have the Secure merchant account ID and it will create something like this.

```html
&lt;form action="https://www.paypal.com/cgi-bin/webscr" method="post"&gt;
&lt;input type="hidden" name="cmd" value="_s-xclick"&gt;
&lt;input type="hidden" name="hosted_button_id" value="numberinhere"&gt;
&lt;input type="image" src="https://www.paypal.com/en_US/i/btn/btn_donateCC_LG.gif" border="0" name="submit" alt="PayPal - The safer, easier way to pay online!"&gt;
&lt;img alt="" border="0" src="https://www.paypal.com/en_US/i/scr/pixel.gif" width="1" height="1"&gt;
&lt;/form&gt;```
where it has the numberinhere there will be a number specific for that button. The buttons are also saved just for you. You can ofcourse just replace the images with something you like.

And for those people who like to know why I did all this&#8230; LessThanDot now also has a [donate page][1].

Enjoy.

 [1]: http://lessthandot.com/donate.php