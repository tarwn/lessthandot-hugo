---
title: 'Learning Ruby on windows: step 1 classes'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-24T06:10:00+00:00
ID: 1270
excerpt: |
  Introduction
  
  I am on my way to learn Ruby (my way). So I randomly try out things I think I might need or think that seem important to me. I try not to look at books to much because I learn best from making mistakes, if you do everything right the fir&hellip;
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows-step-1/
views:
  - 1218
categories:
  - Ruby-on-Rails
tags:
  - ruby
  - rubymine

---
## Introduction

I am on my way to learn Ruby (my way). So I randomly try out things I think I might need or think that seem important to me. I try not to look at books to much because I learn best from making mistakes, if you do everything right the first time you probably have no idea what will happen if you do it wrong. I also try to write tests for everything and see what exceptions I get and why I get them.

## My first class

So I&#8217;m still using [Rubymine][1] (I guess until my trail runs out and I have to go look for something else or if it&#8217;s good enough I might buy it)

So first thing I did was create new class file. I called the file plant.rb and the classname was plant. And I got this error.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/ruby9.png?mtime=1311491523"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/ruby9.png?mtime=1311491523" width="489" height="127" /></a>
</div>

That&#8217;s strange, what does it mean? It means I should have started the name of my class with a capital. None of my tutorials I read mentioned this little fact. But good to know. I guess it is time to read up on Ruby naming conventions.

So I changed the name and wrote a test.

```ruby
require 'rubygems'
gem 'test-unit'
require "test/unit"
require_relative "plant"

class Test_plant &lt; Test::Unit::TestCase

  def test_if_plant_has_name
    my_plant = Plant.new
    assert_equal("test", my_plant.get_name,"Plant has the wrong name")
  end
end```
And of course it fails with a NoMethodError. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/ruby10.png?mtime=1311492562"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/ruby10.png?mtime=1311492562" width="1380" height="291" /></a>
</div>

So I create one.

```ruby
class Plant
  def get_name
    # code here
  end
end```
and run the test again. The test still fails but for a good reason. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/ruby11.png?mtime=1311492794"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/ruby11.png?mtime=1311492794" width="1378" height="289" /></a>
</div>

So lets make the test pass shall we.

```ruby
class Plant
  def get_name
    return "test"
  end
end```
And it does pass.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/ruby12.png?mtime=1311493032"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/ruby12.png?mtime=1311493032" width="1376" height="288" /></a>
</div>

Seems like we got the basics covered. Now I also want to test if I can set the name of my plant.

```ruby
def test_if_name_can_be_set
    my_plant = Plant.new
    my_plant.set_name("new name")
    assert_equal("new name",my_plant.get_name,"Plant has the wrong name")
  end```
I get the NoMethodError on the first run of course, so I add the method.

```ruby
class Plant
  def get_name
    return "test"
  end

  def set_name(name)
    # code here
  end
end```
Run the tests and the first test still passes but the second test still fails. With this message.

> <code class="codespan">Plant has the wrong name.&lt;br />
&lt;"new name"&gt; expected but was&lt;br />
&lt;"test"&gt;.</code>

I guess it is time to add a local variable to me class.

Local variables in ruby are nice since you don&#8217;t have to declare them anywhere. The first use just declares them. Local variables also start with an @ sign. So I changed my class Plant to this.

```ruby
class Plant
  def get_name
    return @name
  end

  def set_name(name)
    @name = name
  end
end```
Which now results in the first test not passing and the second test passing.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/ruby13.png?mtime=1311493941"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/ruby13.png?mtime=1311493941" width="1382" height="349" /></a>
</div>

To mak the first test pass I need a constructor. In Ruby constructors are a method called initialize.

```ruby
class Plant
  def initialize
    @name = "test"
  end

  def get_name
    return @name
  end

  def set_name(name)
    @name = name
  end
end```
And now both tests pass.

## Conclusion

There is a lot more to classes then I have here, but the above just described how coming from a .Net background I would look at it. I found all the things I have in .net and went with it. But of course that is the wrong way of looking at things since Ruby can do so much more with classes. But now that we got the basics covered and we know how to them, we can move on to other things. I found the [pragmatic programmer&#8217;s guide][2] to be a very interesting read fro more advanced things.

 [1]: http://www.jetbrains.com/ruby/
 [2]: http://www.ruby-doc.org/docs/ProgrammingRuby/html/classes.html