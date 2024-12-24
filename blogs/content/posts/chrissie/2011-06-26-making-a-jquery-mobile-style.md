---
title: Making a jQuery Mobile style for a PHPBB forum
author: Christiaan Baes (chrissie1)
type: post
date: 2011-06-26T06:36:00+00:00
ID: 1231
excerpt: |
  Introduction
  
  At Lessthandot we use PHPBB3 as our forum software and it's no secret. It has it's quirks and I don't like the codebase very much but after using it for so long you get used to it. So I decided to make a mobile part for it. The aim was t&hellip;
url: /index.php/webdev/serverprogramming/php/making-a-jquery-mobile-style/
views:
  - 31263
categories:
  - Javascript
  - PHP
tags:
  - jquery mobile
  - jquey
  - mobile
  - phpbb3
  - style

---
## Introduction

At Lessthandot we use PHPBB3 as our forum software and it&#8217;s no secret. It has it&#8217;s quirks and I don&#8217;t like the codebase very much but after using it for so long you get used to it. So I decided to make a mobile part for it. The aim was to make it simple and less bandwidth heavy. 

## I have style, baby

The first thing to do is to create a new style. For this you go to the styles forlder and add a new folder. Then you add 3 more folders to that. Namely: Imageset, template and theme. And you copy the cfg files from another style over and change them a little. You need 4 cfg files. you nees style.cfg in your new style folder and you need imageset.cfg, template.cfg and theme.cfg in their respective folders.
  
Then you need at least a few templates to make this work. Let&#8217;s start with overall\_header.html and overall\_footer.html.

```
&lt;html&gt;
&lt;head&gt;
    &lt;link rel="shortcut icon" href="{SITE_URL_MASTER}favicon.ico" type="image/vnd.microsoft.icon" /&gt;
    &lt;link href="{T_STYLESHEET_LINK}" rel="stylesheet" type="text/css" media="screen, projection" /&gt;
    &lt;link href="http://code.jquery.com/mobile/latest/jquery.mobile.min.css" rel="stylesheet" type="text/css" /&gt;
    &lt;script src="http://code.jquery.com/jquery-1.6.1.min.js"&gt;&lt;/script&gt;
    &lt;script src="http://code.jquery.com/mobile/latest/jquery.mobile.min.js"&gt;&lt;/script&gt;
    &lt;script type="text/javascript" src="{SITE_URL_MASTER}includes/geshicode.js"&gt;&lt;/script&gt;
    &lt;title&gt;{PAGE_TITLE}&lt;/title&gt;
&lt;/head&gt;
&lt;div id="content"&gt;&lt;a name="start_here"&gt;&lt;/a&gt;
	&lt;div data-role="page" data-theme="b"&gt;
            &lt;div data-role="header"&gt;
                {LOGINMOBILE}
                &lt;h1&gt;Lessthandot - Forum&lt;/h1&gt;
            &lt;/div&gt;
            &lt;div data-role="navbar"&gt;
                &lt;ul&gt;
                  &lt;li&gt;&lt;a href="search.php?search_id=newposts&style=5"&gt;New posts&lt;/a&gt;&lt;/li&gt;
                  &lt;li&gt;&lt;a href="search.php?search_id=active_topics&style=5"&gt;Active posts&lt;/a&gt;&lt;/li&gt;
                  &lt;li&gt;&lt;a href="viewonline.php?style=5"&gt;View online&lt;/a&gt;&lt;/li&gt;
                &lt;/ul&gt;
            &lt;/div&gt;
            &lt;div data-role="content"&gt;
            
```
```
&lt;/div&gt; &lt;!-- content --&gt;
            &lt;div data-role="footer" class="ui-bar"&gt;
                &lt;a href="index.php?style=5" data-role="button"&gt;Forum&lt;/a&gt;
                &lt;a href="{SITE_URL_MASTER}mobileindex.php" data-role="button"&gt;Blogs&lt;/a&gt;
            &lt;/div&gt;
        &lt;/div&gt; &lt;!-- page --&gt;
    &lt;/body&gt;
&lt;/html&gt;```
The first thing people see when they enter phpbb is the forumlist. So we add that to the mix. Here is index\_body.html and forumlist\_body.html.

```
&lt;!-- INCLUDE overall_header.html --&gt;

&lt;!-- INCLUDE forumlist_body.html --&gt;

&lt;!-- INCLUDE overall_footer.html --&gt;```
```
&lt;h3&gt;Forums&lt;/h3&gt;
&lt;ul data-role="listview" data-theme="a"&gt;
&lt;!-- BEGIN forumrow --&gt;
	&lt;!-- IF not forumrow.S_IS_CAT --&gt;
		&lt;li&gt;&lt;a href="{forumrow.U_VIEWFORUM}"&gt;{forumrow.FORUM_NAME}&lt;/a&gt;&lt;/li&gt;
    &lt;!-- ENDIF --&gt;
&lt;!-- END forumrow --&gt;
&lt;/ul&gt;
```
And here is the result. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/jquerymobile5.png?mtime=1309075683"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/jquerymobile5.png?mtime=1309075683" width="413" height="781" /></a>
</div>

I pick the above style by adding style= and it&#8217;s id to the URL. The above does not work when I&#8217;m not logged in however which is a shame but I&#8217;m looking in to that. So first I had to install and activate the style. you can do that in the administrator part of PHPBB.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/Stylesphpbb.png?mtime=1309076137"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/Stylesphpbb.png?mtime=1309076137" width="980" height="303" /></a>
</div>

If you then look at the URL of preview of the mobile style you will see a parameter style= and an id, that&#8217;s the id you want.

What you then also need is a viewforum_body.html.

```
&lt;!-- INCLUDE overall_header.html --&gt;
&lt;h2&gt;{FORUM_NAME}&lt;/h2&gt;
&lt;!-- IF S_HAS_SUBFORUM --&gt;
	&lt;!-- INCLUDE forumlist_body.html --&gt;
&lt;!-- ENDIF --&gt;

&lt;h3&gt;Posts&lt;/h3&gt;
&lt;ul data-role="listview" data-theme="b"&gt;
&lt;!-- BEGIN topicrow --&gt;
	&lt;li&gt;&lt;!-- IF topicrow.S_UNREAD_TOPIC --&gt;&lt;a href="{topicrow.U_NEWEST_POST}"&gt;{NEWEST_POST_IMG}&lt;/a&gt; &lt;!-- ENDIF --&gt;&lt;a href="{topicrow.U_VIEW_TOPIC}"&gt;{topicrow.TOPIC_TITLE}&lt;/a&gt;&lt;/li&gt;
&lt;!-- END topicrow --&gt;
&lt;/ul&gt;
&lt;br /&gt;
&lt;!-- IF PAGINATION_MOBILE --&gt;
&lt;div data-role="navbar"&gt;
    &lt;ul&gt;
        {PAGINATION_MOBILE}
    &lt;/ul&gt;
&lt;/div&gt;
&lt;!-- ENDIF --&gt;
    
&lt;!-- INCLUDE overall_footer.html --&gt;```
And you need aonther way for the pagination to work. So I added a function to functions.php.

Which looks like this.

```php
function generate_mobile_pagination($base_url, $num_items, $per_page, $start_item)
{
	global $template, $user;

	// Make sure $per_page is a valid value
	$per_page = ($per_page &lt;= 0) ? 1 : $per_page;

	$total_pages = ceil($num_items / $per_page);

	if ($total_pages == 1 || !$num_items)
	{
		return false;
	}

	$on_page = floor($start_item / $per_page) + 1;
	$url_delim = (strpos($base_url, '?') === false) ? '?' : '&amp;';

    $page_string = ($on_page == 1)? '&lt;li&gt;&lt;a href="' . $base_url . '"  class="ui-btn-active"&gt;1&lt;/a&gt;&lt;/li&gt;': '&lt;li&gt;&lt;a href="' . $base_url . '"&gt;1&lt;/a&gt;&lt;/li&gt;';
    
	if ($total_pages &gt; 4)
	{
		$start_cnt = min(max(1, $on_page - 2), $total_pages - 4);
		$end_cnt = max(min($total_pages, $on_page + 2), 5);

		for ($i = $start_cnt + 1; $i &lt; $end_cnt; $i++)
		{
			$page_string .= ($i == $on_page) ? '&lt;li&gt;&lt;a href="' . $base_url . '"  class="ui-btn-active"&gt;'. $i .'&lt;/a&gt;&lt;/li&gt;' : '&lt;li&gt;&lt;a href="' . $base_url . "{$url_delim}start=" . (($i - 1) * $per_page) . '"&gt;' . $i . '&lt;/a&gt;&lt;/li&gt;';
		}
	}
	else
	{
		for ($i = 2; $i &lt; $total_pages; $i++)
		{
			$page_string .= ($i == $on_page) ? '&lt;li&gt;&lt;a href="' . $base_url . '"  class="ui-btn-active"&gt;'. $i .'&lt;/a&gt;&lt;/li&gt;' : '&lt;li&gt;&lt;a href="' . $base_url . "{$url_delim}start=" . (($i - 1) * $per_page) . '"&gt;' . $i . '&lt;/a&gt;&lt;/li&gt;';
		}
	}

	$page_string .= ($on_page == $total_pages) ? '&lt;li&gt;&lt;a href="' . $base_url . '"  class="ui-btn-active"&gt;'. $total_pages .'&lt;/a&gt;&lt;/li&gt;' : '&lt;li&gt;&lt;a href="' . $base_url . "{$url_delim}start=" . (($total_pages - 1) * $per_page) . '"&gt;' . $total_pages . '&lt;/a&gt;&lt;/li&gt;';

	return $page_string;
}```
you need to add the variable PAGINATION_MOBILE also to viewtopic.php and viewforum.php since they both will use it and hand it over to the template.

Here is a view of viewtopic.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/jquerymobile6.png?mtime=1309076721"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/jquerymobile6.png?mtime=1309076721" width="413" height="781" /></a>
</div>

And here is a view of the pagination.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/jquerymobile7.png?mtime=1309076735"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/jquerymobile7.png?mtime=1309076735" width="413" height="781" /></a>
</div>

Now all we need to do is show the topic. We will need to copy bbcode.html for that (just copy it from one of the other themes). And we need a viewtopic_body.html.

```
&lt;!-- INCLUDE overall_header.html --&gt;
&lt;h2&gt;{TOPIC_TITLE}&lt;/h2&gt;
&lt;!-- BEGIN postrow --&gt;
    &lt;h3&gt;{postrow.POST_SUBJECT}&lt;/h3&gt;
	&lt;p class="author"&gt;&lt;strong&gt;{postrow.POST_AUTHOR}&lt;/strong&gt; {L_POSTED_ON_DATE} {postrow.POST_DATE} &lt;/p&gt;
	&lt;div class="content"&gt;{postrow.MESSAGE}&lt;/div&gt;
	&lt;!-- IF postrow.SIGNATURE --&gt;&lt;div class="signature"&gt;{postrow.SIGNATURE}&lt;/div&gt;&lt;!-- ENDIF --&gt;
	&lt;hr class="divider" /&gt;
&lt;!-- END postrow --&gt;
&lt;!-- IF PAGINATION_MOBILE --&gt;
&lt;div data-role="navbar"&gt;
    &lt;ul&gt;
        {PAGINATION_MOBILE}
    &lt;/ul&gt;
&lt;/div&gt;
&lt;!-- ENDIF --&gt;

&lt;!-- INCLUDE overall_footer.html --&gt;```
With this as the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/jquerymobile8.png?mtime=1309077017"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/jquery/jquerymobile8.png?mtime=1309077017" width="413" height="781" /></a>
</div>

And now all that is left to do is to make styles for new posts, active posts and view online. Which are easy once you understand what I did above.

So this was the readonlyversion of the mobile style, next up will be the to add post reply to it all and login/logout.

## Conclusion

Making a new mobile style for phpbb was reasonably easy so go ahead make your own ;-).