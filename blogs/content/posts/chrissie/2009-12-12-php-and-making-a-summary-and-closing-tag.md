---
title: php and making a summary and closing tags
author: Christiaan Baes (chrissie1)
type: post
date: 2009-12-12T09:00:37+00:00
ID: 648
url: /index.php/webdev/webdesigngraphicsstyling/php-and-making-a-summary-and-closing-tag/
views:
  - 32747
categories:
  - Web Design, Graphics and Styling
tags:
  - close tags
  - lessthandot
  - php

---
## The problem

for this blog we needed to make some adjustments to the code. One of them was the summary. Because we don&#8217;t want our bloggers to write excerpts by themselves, we decided to do it for them (mainly tarwn). 

To do this we take the original blogpost and cut it off at 600 characters. This works pretty well in php, since there are some functions for this. And this is what we have so far.

```php
function closetags($html, $blockquote=true){
	$doc = new DOMDocument();
	@$doc-&gt;loadHTML($html);
	$text = $doc-&gt;saveXML();
	$text = str_replace('&lt;?xml version="1.0" standalone="yes"?&gt;',"",$text);
	$text = str_replace('&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "http://www.w3.org/TR/REC-html40/loose.dtd"&gt;',"",$text);
	$text = str_replace('&lt;body&gt;',"",$text);
	$text = str_replace('&lt;/body&gt;',"",$text);
	$text = str_replace('&lt;html&gt;',"",$text);
	$text = str_replace('&lt;/html&gt;',"",$text);
	$text = preg_replace('/&lt;div class="codeheader"&gt;(&lt;span&gt;[^&lt;]+&lt;/span&gt;).*&lt;/div&gt;/','&lt;div class="codeheader"&gt;1 Sample Code (See Article for Rest)&lt;/div&gt;&lt;/div&gt;',$text);

	if($blockquote)
	{
		$text = str_replace('&lt;blockquote&gt;',"&lt;blockquote&gt;&lt;p&gt;",$text);
		$text = str_replace('&lt;/blockquote&gt;',"&lt;/p&gt;&lt;/blockquote&gt;",$text);
	}
	return $text;
}```
this worked pretty wel up untill I had a post that was cutt of like this.

> <code class="codespan">&lt;div class="</code>

The closetags function made it into this.

> <code class="codespan">&lt;div class=""/&gt;</code>

That is not interpreted in the correct way by browers it seems. But the fix was simple and nice, just the way we like them. Of course, it involved some RTFMing for a while.

## The solution

```php
$text = $doc-&gt;saveXML(null, LIBXML_NOEMPTYTAG);```
Now the saveXML will not expand empty tags, which works better for us.

## Sites used.

  * [php.net savexml][1]
  * [php.net libxml constants][2]
  * [How to implement on symfony][3]

 [1]: http://php.net/manual/en/domdocument.savexml.php
 [2]: http://www.php.net/manual/en/libxml.constants.php
 [3]: http://trac.symfony-project.org/ticket/3663