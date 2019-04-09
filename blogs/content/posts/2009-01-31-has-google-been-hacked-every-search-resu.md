---
title: Has Google Been Hacked? Every search result shows This site may harm your computer.
author: SQLDenis
type: post
date: 2009-01-31T12:47:29+00:00
ID: 306
excerpt: |
  What is going on with Google today? Every search I do has This site may harm your computer.
  
  See the image below 
  
  
    
  
  And how ironic is this? Google itself shows up as malware  LOL
    
  
  
  
  Google seemed to have fixed this problem, Took about&hellip;
url: /index.php/itprofessionals/ethicsit/has-google-been-hacked-every-search-resu/
views:
  - 6850
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
tags:
  - google
  - hacked
  - search

---
What is going on with Google today? Every search I do has **This site may harm your computer**.

See the image below 

<a href="http://tinypic.com" target="_blank"><img src="http://i39.tinypic.com/t7ecmq.jpg" border="0" alt="This site may harm your computer" /></a>

And how ironic is this? Google itself shows up as malware LOL

<a href="http://tinypic.com" target="_blank"><img src="http://i41.tinypic.com/2gvr5dv.jpg" border="0" alt="Google Is Malware?" /></a>

Google seemed to have fixed this problem, Took about an hour or so…..wonder if they lost any money during this time

Here is what happened according to the [Official Google Blog][1]

> What happened? Very simply, human error. Google flags search results with the message “This site may harm your computer” if the site is known to install malicious software in the background or otherwise surreptitiously. We do this to protect our users against visiting sites that could harm their computers. We maintain a list of such sites through both manual and automated methods. We work with a non-profit called StopBadware.org to come up with criteria for maintaining this list, and to provide simple processes for webmasters to remove their site from the list.
> 
> We periodically update that list and released one such update to the site this morning. Unfortunately (and here's the human error), the URL of &#8216;/' was mistakenly checked in as a value to the file and &#8216;/' expands to all URLs. Fortunately, our on-call site reliability team found the problem quickly and reverted the file. Since we push these updates in a staggered and rolling fashion, the errors began appearing between 6:27 a.m. and 6:40 a.m. and began disappearing between 7:10 and 7:25 a.m., so the duration of the problem for any particular user was approximately 40 minutes.

 [1]: http://googleblog.blogspot.com/2009/01/this-site-may-harm-your-computer-on.html