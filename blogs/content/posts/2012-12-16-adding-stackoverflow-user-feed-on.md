---
title: Adding Stackoverflow user feed to your homepage
author: Rob Earl
type: post
date: 2012-12-16T16:48:00+00:00
excerpt: |
  Following on from a comment on a previous post, this post will demonstrate how to use XML::Feed and Template::Toolkit to place your recent Stackoverflow activity onto your own web page. Aptly, this was aided by a stackoverflow question!
  
  This time we&hellip;
url: /index.php/webdev/serverprogramming/adding-stackoverflow-user-feed-on/
views:
  - 5662
rp4wp_auto_linked:
  - 1
categories:
  - Perl
  - Server Programming

---
Following on from a comment on a [previous post][1], this post will demonstrate how to use XML::Feed and Template::Toolkit to format your recent Stackoverflow activity, suitable for including on your own web page. Aptly, this was aided by a [stackoverflow question][2]!

This time we can use XML::Feed both to get and parse the feed:

<pre>use XML::Feed;
my $feed = XML::Feed-&gt;parse(URI-&gt;new('http://stackoverflow.com/feeds/user/1691146'));</pre>

We need to adapt the template to account for XML::Feed&#8217;s differences: 

<pre>my $template = &lt;&lt;"TEMPLATE";
[% feed.title %]
 
Activity:
[% FOREACH item = feed.items %]
[% item.issued.dmy %] [% item.issued.hms %]t[% item.title %]
[% END %]
TEMPLATE</pre>

And the template toolkit invocation:

<pre>$tt-&gt;process( $template, { 'feed' =&gt; $feed } )
  or die $tt-&gt;error();</pre>

Full example:

<pre>#!/usr/bin/perl

use strict;
use warnings;

use XML::Feed;
use Template;

my $stackoverflow_id = 1691146;
my $stackoverflow_url = "http://stackoverflow.com/feeds/user/$stackoverflow_id";

my $template = &lt;&lt;"TEMPLATE";
[% feed.title %]
 
Activity:
[% FOREACH item = feed.items %]
[% item.issued.dmy %] [% item.issued.hms %]t[% item.title %]
[% END %]
TEMPLATE

my $tt = Template-&gt;new( 'STRICT' =&gt; 1 )
  or die "Failed to load template: $Template::ERRORn";

my $feed = XML::Feed-&gt;parse(URI-&gt;new($stackoverflow_url));

$tt-&gt;process( $template, { 'feed' =&gt; $feed } )
  or die $tt-&gt;error();</pre>

This is still a really simple script with added support for Atom feeds.

 [1]: /index.php/WebDev/perl/taming-rss-feeds-with-xml
 [2]: http://stackoverflow.com/q/13897382/1691146