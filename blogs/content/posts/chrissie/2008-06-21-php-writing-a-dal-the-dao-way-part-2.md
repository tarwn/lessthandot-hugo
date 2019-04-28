---
title: 'PHP: Writing a DAL the DAO way part 2'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-21T05:51:32+00:00
ID: 67
url: /index.php/webdev/serverprogramming/php/php-writing-a-dal-the-dao-way-part-2/
views:
  - 3325
categories:
  - PHP

---
[Part one of this series.][1]

I&#8217;m fairly new to the php development thing. But I&#8217;m learning fast. Of course I&#8217;m having to deal with newbie problems along the way. Today I was adding a class to my DAL. Nothing special, mainly copy paste stuff. But php wasn&#8217;t being very cooperative, it kept showing me an empty page whenever I added (require_once(&#8220;filepath&#8221;)) that file to my factory. Not very amusing. So after a little searching I found out that php doesn&#8217;t support classes with the same name. But how am I supposed to know that the same name already exists somewhere? In short you can&#8217;t. 

But there are solutions to the problem. 

First: if you have php version 5.3 or higher you can use namespaces which is [nicely explained by David][2].

Second: if you have a version less then 5.3 you can use a poormans version, and should, of namespaces. Meaning, you have to add things to your classname and seperate them with an underscore. Something like 

```php
class LTD_User{}```

 [1]: /index.php/WebDev/ServerProgramming/php-wrtting-a-dal-the-dao-way-part-1
 [2]: http://blog.agoraproduction.com/index.php?/archives/47-PHP-Namespaces-Part-1-Basic-usage-gotchas.html