---
title: Using a Perl Singleton to Share Values Between Objects
author: Rob Earl
type: post
date: 2012-09-12T14:54:00+00:00
ID: 1719
excerpt: |
  It's been quite a while since I last posted here. Since my last entry I got a new job,  got married, went on honeymoon (got back yesterday!) and spent quite a lot of time maintaining legacy Perl code.
  
  One thing I see a lot is sharing objects by passi&hellip;
url: /index.php/webdev/serverprogramming/using-a-perl-singleton/
views:
  - 6224
rp4wp_auto_linked:
  - 1
categories:
  - Perl
  - Server Programming
tags:
  - design patterns
  - perl
  - singleton

---
It&#8217;s been quite a while since I last posted here. Since my last entry I got a new job, got married, went on honeymoon (got back yesterday!) and spent quite a lot of time maintaining legacy Perl code.

One thing I see a lot is sharing objects by passing them to the constructor of a new object. This is fine until you&#8217;re dealing with complicated sets of objects within objects. 

```perl
#!/usr/bin/perl

use strict;

# Read some variables out of a configuration file.
my $config = get_config($config_file);

# Create a new object which contains an object which uses some values from the config.
my $object = MyObject->new($config);
$object->do_something();

package MyObject;

sub new {
    my $class = shift;
    my $config = shift;

    # Store the config incase we create any objects which need it.
    my $self = { 'config' => $config };

    return bless $self, $class;
}

sub do_something {
    my $self = shift;

    my $object2 = MyObject2->new($self->{'config'});

    return $object2->do_something_else();
}
```
MyObject is now holding a reference to the config object which it doesn&#8217;t need directly. This will only get worse if MyObject2 needs another object which requires the config object, etc.

Instead we could use a singleton object:

```perl
package MyConfigObject;

use strict;
use Carp;

my $config = undef;

sub new {

    if(!$config) {
        my $class = shift;
        my $config_file = shift;

        croak unless $config_file;

        $config = bless {}, $class;
        $config->parse_file($config_file);
    }

    return $config;
}
```
The example above changes to:

```perl
#!/usr/bin/perl

use strict;

# Create a new object to hold the values from our config file.
my $config = MyConfigObject->new($config_file);

# Create a new object which contains an object which uses some values from the config.
my $object = MyObject->new();
$object->do_something();

package MyObject;

sub new {
    my $class = shift;

    return bless {}, $class;
}

sub do_something {
    my $self = shift;

    my $object2 = MyObject2->new();

    return $object2->do_something_else();
}

package MyObject2;

sub new {
    # Get the config object
    my $config = MyConfigObject->new();

    # Do some stuff with it!
}
```
Whenever we need to use a value from the config file, we create a new MyConfigObject and get the instance we already created without having to reparse the config file or maintain complicated links through parent objects.