---
title: Rubymine getting the test-unit to work better.
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-23T10:41:00+00:00
ID: 1269
excerpt: |
  Introduction
  
  As you might have read I had a little problem with my first test and rubymine. It ran the test but it did complain and did not show the pretty results I like and want.
  
  The problem
url: /index.php/webdev/serverprogramming/rubyonrails/rubymine-getting-the-test-unit/
views:
  - 5744
categories:
  - Ruby-on-Rails
tags:
  - ruby
  - rubymine

---
## Introduction

As you might have read I had a little problem with my first test and rubymine. It ran the test but it did complain and did not show the pretty results I like and want.

## The problem

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rubymine5.png?mtime=1311412915"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rubymine5.png?mtime=1311412915" width="1035" height="762" /></a>
</div>

Of course it says how to solve it but not really clearly. 

> <span class="MT_red"></span>Please install &#8216;test-unit&#8217; gem and activate it on runtime.

## The solution

I kind of found the [solution here][1]. But not sure how my brain figured that out.

You need to install the gem and you can do that via the ruby command prompt.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rubymine6.png?mtime=1311424601"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rubymine6.png?mtime=1311424601" width="231" height="115" /></a>
</div>

And then run this gem command.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rubymine7.png?mtime=1311424611"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rubymine7.png?mtime=1311424611" width="707" height="423" /></a>
</div>

And after I found that I was happy and read my mail where I found this.

> Author: Luis Lavena 
> 
> To solve the Test integration of RubyMine, you need to install test-unit gem, as mentioned in red in the console.
  
> Just open &#8220;Command Prompt with Ruby&#8221; and do:
  
> gem install test-unit
  
> It should install the gem, which then should enable the proper Test/Unit support on RubyMine.
  
> Cheers.

Oh well. I&#8217;ll remember to blog about it and then wait for someone to post the solution ;-).

And this is what it looks like when it works.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rubymine8.png?mtime=1311424977"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rubymine8.png?mtime=1311424977" width="1035" height="762" /></a>
</div>

## Conclusion

It works. And the ruby community is awesome ;-).

 [1]: http://www.ruby-forum.com/topic/182629