---
title: Taming RSS Feeds with XML::RSS and Template::Toolkit
author: Rob Earl
type: post
date: 2012-09-14T11:01:00+00:00
ID: 1722
excerpt: |
  This is a remarkably simple trick which I've found very handy. With a few lines of Perl you can take any RSS feed and format it to your liking.
  
  Get the Feed
  
  You can do this using LWP::Simple:
  
  
  use LWP::Simple;
  
  my $feed_url = 'http://feeds.b&hellip;
url: /index.php/webdev/serverprogramming/taming-rss-feeds-with-xml/
views:
  - 8793
rp4wp_auto_linked:
  - 1
categories:
  - Perl
  - Server Programming
tags:
  - feed
  - perl
  - rss
  - template
  - toolkit
  - xml

---
This is a remarkably simple trick which I&#8217;ve found very handy. With a few lines of Perl you can take any RSS feed and format it to your liking.

# 

# Get the Feed

You can do this using [LWP::Simple][1]:

```perl
use LWP::Simple;

my $feed_url = 'http://feeds.bbci.co.uk/news/rss.xml';
my $feed = get($feed_url)
        or die ("Failed to fetch feed.");
```
# 

# Process the Raw Result

Using [XML::RSS][2], convert the raw feed into a more manageable hash.

```perl
use XML::RSS;

my $rss = XML::RSS->new();
$rss->parse($feed);
```
# 

# Format to Your Liking

[Template::Toolkit][3] can take in a [template][4] and a hash reference of values to substitute into the template.

```perl
# Define a template
my $template = <<"TEMPLATE";
[% channel.title %]

Headlines:
[% FOREACH item = items %]
[% item.pubDate %]t[% item.title %]
[% END %]
TEMPLATE
```
This simple template will take the BBC news feed from above and print out a list of headlines with publication dates.

```perl
my $tt = Template->new()
        or die ("Failed to load template: $Template::ERRORn");

# Combine the template with the processed RSS feed.
$tt->process ( $template, $rss )
        or die $tt->error();
```
# 

# Putting it All Together

```perl
#!/usr/bin/perl
use strict;
use warnings;

use XML::RSS;
use LWP::Simple;
use Template;

##################
# Configuration:
#
##################
my $feed_url = 'http://feeds.bbci.co.uk/news/rss.xml';
my $template = <<"TEMPLATE";
[% channel.title %]

Headlines:
[% FOREACH item = items %]
[% item.pubDate %]t[% item.title %]
[% END %]
TEMPLATE

##################
##################

my $tt = Template->new()
        or die ("Failed to load template: $Template::ERRORn");
my $feed = get($feed_url)
        or die ('Failed to fetch feed.');
my $rss = XML::RSS->new();
$rss->parse($feed);

$tt->process ( $template, $rss )
        or die $tt->error();
```
```bash
rob@arrakis:~/public_html/rss-reader$ perl rss-reader.pl 
BBC News - Home

Headlines:

Fri, 14 Sep 2012 12:15:22 GMT	Kate privacy invasion 'grotesque'

Fri, 14 Sep 2012 11:49:10 GMT	US missions on film protest alert

Fri, 14 Sep 2012 12:25:25 GMT	Alps attack girl returning to UK

Fri, 14 Sep 2012 10:17:03 GMT	Woman is held after car body find
```
In this example the RSS feed and template are defined in code but they can just as easily be defined in files or a database allowing for changes/additions without deploying new code.

 [1]: http://search.cpan.org/~gaas/libwww-perl-6.04/lib/LWP/Simple.pm
 [2]: http://search.cpan.org/~kellan/XML-RSS-1.05/lib/RSS.pm
 [3]: http://template-toolkit.org/
 [4]: http://template-toolkit.org/docs/manual/Syntax.html