---
title: Encoding to UTF-8 in php
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-23T09:48:00+00:00
ID: 1008
excerpt: This week we saw some strange symbols on our launchpad in one of the excerpts of a post. We did however not see this on the blog part of our site. So there must be a difference between the two. Here is how I solved it.
url: /index.php/webdev/serverprogramming/php/encoding-to-utf-8-in/
views:
  - 5120
categories:
  - AJAX
  - PHP
tags:
  - cp-1251
  - encoding
  - php
  - utf-8

---
## Introduction

This week we saw some strange symbols on our launchpad in one of the excerpts of a post. We did however not see this on the blog part of our site. So there must be a difference between the two.

## The solution

We had several ideas but most of them had to do with encoding. 

This is what it looked like.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/encoding/encoding1.png?mtime=1295782780"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/encoding/encoding1.png?mtime=1295782780" width="673" height="173" /></a>
</div>

The first thing I tried after some google was the utf8 encode command.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/encoding/encoding2.png?mtime=1295782793"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/encoding/encoding2.png?mtime=1295782793" width="690" height="164" /></a>
</div>

But there was a little problem. the little single quote (or whatever it was) was no longer there. So I went out to find out what the problem was. Since b2evo did not have this problem I searched how they solved it and I found it. 

```php
/**
 * Convert all non ASCII chars (except if UTF-8, GB2312 or CP1251) to &#nnnn; unicode references.
 * Also convert entities to &#nnnn; unicode references if output is not HTML (eg XML)
 *
 * Preserves &lt; &gt; and quotes.
 *
 * fplanque: simplified
 * sakichan: pregs instead of loop
 */
function convert_chars( $content, $flag = 'html' )
{
    global $b2_htmltrans, $evo_charset;
 
    /**
     * Translation of invalid Unicode references range to valid range.
     * These are Windows CP1252 specific characters.
     * They would look weird on non-Windows browsers.
     * If you've ever pasted text from MSWord, you'll understand.
     *
     * You should not have to change this.
     */
    static $b2_htmltranswinuni = array(
        '&#128;' =&gt; '&#8364;', // the Euro sign
        '&#130;' =&gt; '&#8218;',
        '&#131;' =&gt; '&#402;',
        '&#132;' =&gt; '&#8222;',
        '&#133;' =&gt; '&#8230;',
        '&#134;' =&gt; '&#8224;',
        '&#135;' =&gt; '&#8225;',
        '&#136;' =&gt; '&#710;',
        '&#137;' =&gt; '&#8240;',
        '&#138;' =&gt; '&#352;',
        '&#139;' =&gt; '&#8249;',
        '&#140;' =&gt; '&#338;',
        '&#142;' =&gt; '&#382;',
        '&#145;' =&gt; '&#8216;',
        '&#146;' =&gt; '&#8217;',
        '&#147;' =&gt; '&#8220;',
        '&#148;' =&gt; '&#8221;',
        '&#149;' =&gt; '&#8226;',
        '&#150;' =&gt; '&#8211;',
        '&#151;' =&gt; '&#8212;',
        '&#152;' =&gt; '&#732;',
        '&#153;' =&gt; '&#8482;',
        '&#154;' =&gt; '&#353;',
        '&#155;' =&gt; '&#8250;',
        '&#156;' =&gt; '&#339;',
        '&#158;' =&gt; '&#382;',
        '&#159;' =&gt; '&#376;'
    );
 
    // Convert highbyte non ASCII/UTF-8 chars to urefs:
    if( ! in_array(strtolower($evo_charset), array( 'utf8', 'utf-8', 'gb2312', 'windows-1251') ) )
    { // This is a single byte charset
        // fp&gt; why do we actually bother doing this:?
        $content = preg_replace_callback(
            '/[x80-xff]/',
            create_function( '$j', 'return "&#".ord($j[0]).";";' ),
            $content);
    }
 
    ... rest of code in b2evo source.```
That is one hell of a solution and it would require a fari bit of research to find out why you need all this. It will need a good knowledge to see the difference between the codecs and how you can solve it. I did not feel the need to do this and just used the b2evo code. And that solved the problem.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/encoding/encoding3.png?mtime=1295782805"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/encoding/encoding3.png?mtime=1295782805" width="682" height="167" /></a>
</div>

## Conclusion

No need to reinvent the wheel. Use what is there and credit the maker and move on. But I do understand what it does and why it does it and that is important.