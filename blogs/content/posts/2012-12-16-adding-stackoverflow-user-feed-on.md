---
title: Adding Stackoverflow user feed to your homepage
author: Rob Earl
type: post
date: 2012-12-16T16:48:00+00:00
ID: 1863
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

```perl
use XML::Feed;
my $feed = XML::Feed->parse(URI->new('http://stackoverflow.com/feeds/user/1691146'));
```

We need to adapt the template to account for XML::Feed's differences: 

```perl
my $template = <<"TEMPLATE";
[% feed.title %]
 
Activity:
[% FOREACH item = feed.items %]
[% item.issued.dmy %] [% item.issued.hms %]t[% item.title %]
[% END %]
TEMPLATE
```

And the template toolkit invocation:

```perl
$tt->process( $template, { 'feed' => $feed } )
  or die $tt->error();
```

Full example:

```perl
#!/usr/bin/perl

use strict;
use warnings;

use XML::Feed;
use Template;

my $stackoverflow_id = 1691146;
my $stackoverflow_url = "http://stackoverflow.com/feeds/user/$stackoverflow_id";

my $template = <<"TEMPLATE";
[% feed.title %]
 
Activity:
[% FOREACH item = feed.items %]
[% item.issued.dmy %] [% item.issued.hms %]t[% item.title %]
[% END %]
TEMPLATE

my $tt = Template->new( 'STRICT' => 1 )
  or die "Failed to load template: $Template::ERRORn";

my $feed = XML::Feed->parse(URI->new($stackoverflow_url));

$tt->process( $template, { 'feed' => $feed } )
  or die $tt->error();
```

This is still a really simple script with added support for Atom feeds.

 [1]: /index.php/WebDev/perl/taming-rss-feeds-with-xml
 [2]: http://stackoverflow.com/q/13897382/1691146