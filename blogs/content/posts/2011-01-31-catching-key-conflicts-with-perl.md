---
title: Catching Key Conflicts with Perl’s Class::DBI
author: Rob Earl
type: post
date: 2011-01-31T09:52:00+00:00
ID: 1022
excerpt: |
  When inserting to a database I find it more convenient to catch a key conflict than to perform a select in order to check for duplicates. Defining a column as unique will ensure data integrity at the lowest level.
  
  create table customer (
  id int auto&hellip;
url: /index.php/webdev/serverprogramming/catching-key-conflicts-with-perl/
views:
  - 2994
rp4wp_auto_linked:
  - 1
categories:
  - Server Programming

---
When inserting to a database I find it more convenient to catch a key conflict than to perform a select in order to check for duplicates. Defining a column as unique will ensure data integrity at the lowest level.

```sql
create table customer (
id int auto_increment, 
name varchar(20), 
address varchar(50), 
email varchar(50), 
constraint primary key(id), 
unique(email)
);
```

Catching a user attempting to register with an email address which is already in the database is fairly simple when using DBI:

```perl
my $row = $dbh->do(qq{INSERT INTO customer ( name, address, email ) VALUES (?,?,?);}, undef, ("Rob", "Rob's House", "my@email.com")); # Returns undef on error.
&error($dbh->errstr) unless $row; # Forward to error handler. 
```

[Class::DBI][1] is a database abstraction Perl module. It wasn't immediately apparent to me how to handle a key conflict when using it. After a little investigation I found key violations will trigger an exception which makes it fairly simple to handle:

```perl
eval
{
        $new_cust = Customer->insert({name=>"Rob", address=>"Rob's House", email=>"my@email.com" }); # Insert using Class::DBI constructor.
}; &error($@) if $@; # Forward to error handler.

```
The eval block will catch the thrown exception, allowing you to gracefully handle it through your own subroutine.

 [1]: http://search.cpan.org/~tmtm/Class-DBI-v3.0.17/lib/Class/DBI.pm