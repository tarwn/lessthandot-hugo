---
title: 'Learning Ruby on windows: step 1.2 interfaces'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-24T10:48:00+00:00
ID: 1272
excerpt: |
  Introduction
  
  Since I come from statically typed language the next thing to look for are interfaces. An interface in .Net is more or less a contract or a way to make multiple inheritance possible whatever you like. There are people who can explain it&hellip;
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows-step-3/
views:
  - 12712
categories:
  - Ruby-on-Rails
tags:
  - ruby
  - rubymine

---
## Introduction

Since I come from statically typed language the next thing to look for are interfaces. An interface in .Net is more or less a contract or a way to make multiple inheritance possible whatever you like. [There are people who can explain it better than me.][1]. And there is always Google. But if you look for interfaces in ruby you will not find them. Because you just don&#8217;t need them. The following is how I see it, so I might be wrong. 

## Interfaces

And why don&#8217;t you need them? Because the compiler does not check for your methods if they are there at compiletime. You will only know if a method is not there on a certain object when you try to use it. And all this because you can extend classes at runtime in ruby. Afterall ruby is dynamic. 

The better way to check if your code works is to use unittests and see if it is all there and if your code will work at runtime. So lets try this.

I first create 2 classes both having the same accessor. Like this.

```ruby
class Plant

  attr_accessor :name

end

class Animal

  attr_accessor :name

end```
Now I write a test to prove my point.

```ruby
require 'rubygems'
gem 'test-unit'
require "test/unit"
require_relative "plant"

class Test_plant &lt; Test::Unit::TestCase

  def test_check_if_Plant_and_Animal_both_have_name
    my_plant = Plant.new
    my_animal = Animal.new
    my_plant.name = "test"
    my_animal.name = "test"
    my_array = [my_animal,my_plant]
    my_array.each { |x| assert_equal("test",x.name,"The name is not test")}
  end
end```
Rubymine proves I did 2 assertions.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/ruby14.png?mtime=1311511252"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/ruby14.png?mtime=1311511252" width="1035" height="762" /></a>
</div>

You will soon find that if you do this

```
require 'rubygems'
gem 'test-unit'
require "test/unit"
require_relative "plant"

class Test_plant &lt; Test::Unit::TestCase

  def test_check_if_Plant_and_Animal_both_have_name
    my_plant = Plant.new
    my_animal = Animal.new
    my_plant.name = "test"
    my_animal.name = "test2"
    my_array = [my_animal,my_plant]
    my_array.each { |x| assert_equal("test",x.name,"The name is not test")}
  end
end```
One assert will fail.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/ruby15.png?mtime=1311511469"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/ruby15.png?mtime=1311511469" width="1035" height="841" /></a>
</div>

## Conclusion

You don&#8217;t really need interfaces in ruby, but you need a good testsuite to test your theories. Because of the dynamic nature of ruby you have no idea what your class will be at runtime, someone of your code might have changed the behavior of your class and that might bite you. But it can also be handy from time to time.

 [1]: http://ondotnet.com/pub/a/dotnet/2003/06/30/interfaces.html