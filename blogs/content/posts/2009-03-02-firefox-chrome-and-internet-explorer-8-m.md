---
title: FireFox, Chrome and Internet Explorer 8 Memory Usage, IE8 Uses More Memory Than Chrome and FireFox Combined
author: SQLDenis
type: post
date: 2009-03-02T13:00:22+00:00
url: /index.php/webdev/webdesigngraphicsstyling/firefox-chrome-and-internet-explorer-8-m/
views:
  - 14748
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - chrome
  - firefox
  - google
  - ie8
  - internet
  - internet explorer 8
  - memory leak
  - mozilla
  - web

---
I decided to do a little test on one of my machines with the three browsers I use most. In each browser I opened up [Gmail][1], [Google Reader][2] and [CNNMoney][3].
  
I did this on Friday and did not look at the memory usage until Monday. After a little less than 3 days, we get the following numbers

**Chromium 1.0.154.48**
  
180,157k

**IE 8.00.6001.18372**
  
362,223k

**Firefox 3.0.4**
  
100,148k

Looking at these numbers makes you think that Google wrote Gmail and Google Reader in such a way that it on purpose made Internet Explorer leak memory like a bucket without a bottom.

<pre>If IE 
   then leak memory 
else 
   behave normally</pre>

I don&#8217;t know but this is just crazy, don&#8217;t come to me either saying that IE8 is beta, so what, this is insane! IE8 uses 362MB while FireFox 3 only uses 100MB. Chrome compared to Firefox uses almost double the memory. Chrome + FireFox combine still use 80MB less than Internet Explorer 8

Below is a screen shot of what memory usage looks like

[<img src="http://farm4.static.flickr.com/3594/3322981762_be28114019_o.jpg" width="654" height="192" alt="Browsers" />][4]

 [1]: http://mail.google.com/mail/?tab=ym
 [2]: http://www.google.com/reader/
 [3]: http://money.cnn.com/?cnn=yes
 [4]: http://www.flickr.com/photos/denisgobo/3322981762/ "FireFox, Chrome and Internet Explorer 8 Memory Usage"