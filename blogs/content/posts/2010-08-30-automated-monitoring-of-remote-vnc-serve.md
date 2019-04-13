---
title: Automated Monitoring of Remote VNC Servers
author: Rob Earl
type: post
date: 2010-08-30T10:42:38+00:00
ID: 891
excerpt: |
  For the duration of the Edinburgh Festival Fringe I'm responsible for a couple of Samsung MagicNet plasma screens which run 24/7 in the windows outside a venue. They're essentially 32" screens with a built in PC running Windows XP and a VNC server. They&hellip;
url: /index.php/webdev/serverprogramming/automated-monitoring-of-remote-vnc-serve/
views:
  - 11418
rp4wp_auto_linked:
  - 1
categories:
  - Server Admin
  - Server Programming

---
For the duration of the Edinburgh Festival Fringe I'm responsible for a couple of Samsung MagicNet plasma screens which run 24/7 in the windows outside a venue. They're essentially 32″ screens with a built in PC running Windows XP and a VNC server. They're pretty flexible as you can set them to open a webpage fullscreen then display just about anything.

Unfortunately, they're prone to faults and I have no real way of knowing when one occurs until I attempt to VNC into (or walk passed) one of them which brings me to the purpose of this post: How to ensure your remote VNC sessions are doing what they're supposed to.

```perl
#!/usr/bin/perl

use Net::VNC;
use strict;

my $vnc;
my $path = "/home/rob/Desktop";
#my $path = "screens/";
#my $path = "/var/www/vnc-monitoring";
#my $path = "/home/rob/public_html/vnc-monitoring";

open(LOG, ">>monitoring.log"); # Open the log file.

open(SERVERS , "vnc-servers"); # Open the list of VNC servers, one IP,password per line:
                               # 192.168.0.2,password
                               # 192.168.0.3
                               # 192.168.0.4,anotherpassword
while(<SERVERS>)
{
	my ($address,$password) = split(/,/,$_);
	$address =~ s/r//g;
	$address =~ s/n//g;
	$password =~ s/r//g;
	$password =~ s/n//g;

	$vnc = Net::VNC->new({hostname => $address, password => $password}); # Create a new connection to vnc.
	$vnc->depth(24);

	my $message = "";
	my $image;
	eval
	{
		$vnc->login; # Must be within an eval else the script will bail on error.
		$message = "Connected: ".$address." (".$vnc->name.")";
		$image = $vnc->capture;
	};
	if ($@)
	{
		$message = $@;
		$image = Image::Imlib2->new(200, 200); # Create a blank image to indicate a problem. Could also copy a preset "error" image.
                # We could also send out an email here since we definitely have an issue.
	}

	$address =~ s/./_/g;

	print LOG $message."n";

	$image->save("$path/$address-capture.png");
}

close(LOG);
close(SERVERS);
```
This Perl script uses [Net::VNC][1] to connect to each specified VNC server, capture a screenshot and save it to the specified directory.

From there all you need to do is add it to a Cron job and decide how you're going to monitor the resultant images:

  * Slideshow software on a dedicated screen.
  * Embedded into a web page.
  * Simply saved to your Desktop (Gnome refreshes thumbnails automatically).
  * Maybe even use NotifyOSD (or similar)?

 [1]: http://search.cpan.org/~lbrocard/Net-VNC-0.36/lib/Net/VNC.pm