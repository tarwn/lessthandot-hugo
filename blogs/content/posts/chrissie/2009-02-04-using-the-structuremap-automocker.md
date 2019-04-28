---
title: Using the structuremap Automocker
author: Christiaan Baes (chrissie1)
type: post
date: 2009-02-04T06:32:14+00:00
ID: 314
excerpt: "I was not really convinced in the past that I should use the Structuremap Automocker for any of my tests. I don't like using the container in my tests it makes them way to dependent on something not really needed to run the test. Of course I test to see&hellip;"
url: /index.php/architect/designingsoftware/using-the-structuremap-automocker/
views:
  - 5950
categories:
  - Designing Software
tags:
  - automocking
  - rhinomocks
  - structuremap

---
I was not really convinced in the past that I should use the Structuremap Automocker for any of my tests. I don&#8217;t like using the container in my tests it makes them way to dependent on something not really needed to run the test. Of course I test to see if the container is well configured and if the objects are created by the container when it is well configured (this has saved me several times before). I really only have myself to blame because I didn&#8217;t really try it and only suspected what it was doing. 

Well apparently I was wrong, and lucky for me I read the right blogs. 

Today [Josh Flanagan][1] posted about it on [Los Techies][2], [Auto mocking Explained][3]. Thank you Josh. Of course it is easier for him since he works with Jeremy.

 [1]: http://www.lostechies.com/members/jflanagan/default.aspx
 [2]: http://www.lostechies.com/
 [3]: http://www.lostechies.com/blogs/joshuaflanagan/archive/2009/02/03/auto-mocking-explained.aspx?CommentPosted=true#commentmessage