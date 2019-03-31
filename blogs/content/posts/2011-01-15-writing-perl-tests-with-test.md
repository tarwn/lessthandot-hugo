---
title: Writing Perl Tests with Test::More
author: Rob Earl
type: post
date: 2011-01-15T14:29:00+00:00
excerpt: 'Writing tests for code is just as important as writing the code itself. Time spent writing tests is less time spent tracking down and fixing bugs, making it a great investment. Despite knowing this it can sometimes be hard to force yourself to stop codi&hellip;'
url: /index.php/webdev/serverprogramming/writing-perl-tests-with-test/
views:
  - 12451
rp4wp_auto_linked:
  - 1
categories:
  - Server Programming
tags:
  - oop
  - perl
  - testing

---
Writing tests for code is just as important as writing the code itself. Time spent writing tests is less time spent tracking down and fixing bugs, making it a great investment. Despite knowing this it can sometimes be hard to force yourself to stop coding and write tests. Fortunately, Perl has some modules to make it pretty simple. Here&#8217;s the module we&#8217;re going to be testing:

<pre>#!/usr/bin/perl
 
package MyMaths;
use strict;
 
sub new
{
        my $proto = shift;
        my $class = ref($proto) || $proto;
        my $self = {};
 
        bless($self, $class);
        return $self;
}
 
sub add
{
        my $self = shift;
        my $total = 0;
        foreach my $factor (@_)
        {
                $total += $factor;
        }
        return $total;
}
 
1;</pre>

As you can see, we have a simple class with one method, add, which adds together all the arguments passed to it. We now need to define a test file for this module, called mymaths.t:

<pre>#!/usr/bin/perl
 
use Test::More tests=&gt;6;
use MyMaths;
 
$mymaths = new MyMaths;
is( $mymaths-&gt;add(1,2,3), 6, "1 + 2 + 3 = 6");
is( $mymaths-&gt;add(6,2), 8,"6 + 2 = 8");
is( $mymaths-&gt;add(1,2,3,4), 10, "1 + 2 + 3 + 4 = 10");
is( $mymaths-&gt;add(1,2), 3, "1 + 2 = 3");
is( $mymaths-&gt;add(2), 2, "2 = 2");
is( $mymaths-&gt;add(2,-1), 1, "2 + -1 = 1");</pre>

This test file uses the is() method of the builtin Test::More module. This method takes 3 arguments: the test, the expected result and a description. If a test fails, is() will give some feedback on the test.

**edit:** To run the tests use the \`prove\` utility which is part of the Test::Harness package:

<code class="codespan">prove -v mymaths.t</code>

Run all tests in the current directory with:
  
<code class="codespan">prove -v .</code>

<del cite="/index.php/WebDev/ServerProgramming/writing-perl-tests-with-test#c7568">To run the tests we use another builtin module, Test::Harness. Create tests.pl:</del>

<pre>#!/usr/bin/perl
 
use Test::Harness qw(&runtests);
 
@tests = @ARGV ? @ARGV : &lt;*.t&gt;;
 
runtests @tests;</pre>

<del>This script can either be run with a list of test files as arguments, or with no arguments to run all test files. Running the script results in:</del>

<pre>./mymaths.t .. 
1..6
ok 1 - 1 + 2 + 3 = 6
ok 2 - 6 + 2 = 8
ok 3 - 1 + 2 + 3 + 4 = 10
ok 4 - 1 + 2 = 3
ok 5 - 2 = 2
ok 6 - 2 + -1 = 1
ok
All tests successful.
Files=1, Tests=6,  0 wallclock secs ( 0.01 usr  0.02 sys +  0.02 cusr  0.01 csys =  0.06 CPU)
Result: PASS</pre>

Everything went fine, as expected. Now go back to the original class definition and make a typo on line 22, so it reads = instead of += and rerun the tests:

<pre>./mymaths.t .. 
1..6
not ok 1 - 1 + 2 + 3 = 6
not ok 2 - 6 + 2 = 8
not ok 3 - 1 + 2 + 3 + 4 = 10
not ok 4 - 1 + 2 = 3
ok 5 - 2 = 2
not ok 6 - 2 + -1 = 1

#   Failed test '1 + 2 + 3 = 6'
#   at ./mymaths.t line 7.
#          got: '3'
#     expected: '6'

#   Failed test '6 + 2 = 8'
#   at ./mymaths.t line 8.
#          got: '2'
#     expected: '8'

#   Failed test '1 + 2 + 3 + 4 = 10'
#   at ./mymaths.t line 9.
#          got: '4'
#     expected: '10'

#   Failed test '1 + 2 = 3'
#   at ./mymaths.t line 10.
#          got: '2'
#     expected: '3'

#   Failed test '2 + -1 = 1'
#   at ./mymaths.t line 12.
#          got: '-1'
#     expected: '1'
# Looks like you failed 5 tests of 6.
Dubious, test returned 5 (wstat 1280, 0x500)
Failed 5/6 subtests 

Test Summary Report
-------------------
./mymaths.t (Wstat: 1280 Tests: 6 Failed: 5)
  Failed tests:  1-4, 6
  Non-zero exit status: 5
Files=1, Tests=6,  0 wallclock secs ( 0.03 usr  0.00 sys +  0.03 cusr  0.00 csys =  0.06 CPU)
Result: FAIL</pre>

Most of the tests failed and gave good feedback to help find the problem.

## Summary

Although writing tests can be a chore it is worth doing. If you write tests a little at a time:

  * When you design a class.
  * When you add a method to a class.
  * When you find a bug.

You&#8217;ll develop a good library of tests without too much effort.

Further reading: [Test::More perldoc][1]

 [1]: http://perldoc.perl.org/Test/More.html