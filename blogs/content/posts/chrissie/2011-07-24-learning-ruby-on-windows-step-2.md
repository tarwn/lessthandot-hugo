---
title: 'Learning Ruby on windows: step 1.1 properties/accessors'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-24T06:59:00+00:00
ID: 1271
excerpt: |
  Introduction
  
  In my previous post I used classes and used getters and setters like we did in the good old Java days. But as Jak Charlton (Thanks for all his help). pointed out there are other ways to do the getter-setter thing in Ruby. 
  
  Accessors&hellip;
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows-step-2/
views:
  - 2246
categories:
  - Ruby-on-Rails
tags:
  - ruby
  - rubymine

---
## Introduction

In [my previous post][1] I used classes and used getters and setters like we did in the good old Java days. But as Jak Charlton (Thanks for all his help). pointed out there are other ways to do the getter-setter thing in Ruby. 

## Accessors

First of all there are [accessors][2] . I think an accessor is like an automatic property in .Net. 

In Ruby the accessors like in VB.Net use nicely named backing fields so you can still use them in your code if you like. [This is not true in C#][3]. 

So I can leave in the getter and setter and just add the accessor and everything should just work.

Here is how the class now looks.

```ruby
class Plant
  def initialize
    @name = "test"
  end

  attr_accessor :name

  def get_name
    return @name
  end

  def set_name(name)
    @name = name
  end
end```
And here are my tests to prove it.

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

  def test_if_name_can_be_set
    my_plant = Plant.new
    my_plant.set_name("new name")
    assert_equal("new name",my_plant.get_name,"Plant has the wrong name")
  end

  def test_if_plant_has_name_accessor
    my_plant = Plant.new
    assert_equal("test", my_plant.name,"Plant has the wrong name")
  end

  def test_if_name_can_be_set_by_name_accessor
    my_plant = Plant.new
    my_plant.name = "new name"
    assert_equal("new name",my_plant.name,"Plant has the wrong name")
  end
end```
## Conclusion

Properties are also available in Ruby, but in this case more as automatic properties than as normal properties. They are easier to use than in C#.

 [1]: /index.php/WebDev/ServerProgramming/RubyonRails/learning-ruby-on-windows-step-1
 [2]: http://www.rubyist.net/~slagell/ruby/accessors.html
 [3]: /index.php/All/?p=1272