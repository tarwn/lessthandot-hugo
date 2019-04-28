---
title: Mediawiki and how to get the latest articles
author: Christiaan Baes (chrissie1)
type: post
date: 2008-08-16T05:36:44+00:00
ID: 108
url: /index.php/webdev/serverprogramming/mediawiki-and-how-to-get-the-latest-arti/
views:
  - 7153
categories:
  - Server Programming
tags:
  - mediawiki

---
For some reason the mediawiki database is really hard to get a hold of. In retrospect, it turns out to be a very good reason, but it takes a lot of research to find out why.

Mediawiki does just save a page as you see it on the screen, it saves a page the first time you make it and then it save all the changes. When it is time to show the page it reconstructs the page using the changes and the original, a process that can be a bit time consuming when you don&#8217;t use a cache. 

So I needed to get the latest new articles from the database to show on the launchpad of this site.

First thing you think about is getting the results out of the page table like this

```mysql
SELECT page_title FROM mw_page where page_is_new = 1```
But that doesn&#8217;t really work, because as soon as someone edits a page, it&#8217;s no longer considered new. And there is a total absence of any time related column in that table.

SO after some searching on the net, I came up with this.

```mysql
SELECT rc_title AS page_title
       			FROM mw_recentchanges, mw_page
       			WHERE rc_cur_id = page_id AND rc_new = 1 AND page_is_redirect = 0
       			ORDER BY rc_timestamp DESC
				LIMIT 5```
However, the recentchanges table only holds data for the last 30 days, but that&#8217;s just fine for me.