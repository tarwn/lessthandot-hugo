---
title: 'Learning Ruby on windows: step 1.3 inheritance'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-24T12:33:00+00:00
ID: 1273
excerpt: |
  Introduction
  
  In my previous post I talked about interfaces. In this one I just do simple inheritance with the same classes as in the previous post. More difficult scenarios are possible but We should watch out with inheritance because it can get to c&hellip;
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows-step-4/
views:
  - 8701
categories:
  - Ruby-on-Rails
tags:
  - ruby
  - rubymine

---
## Introduction

In my previous post [I talked about interfaces][1]. In this one I just do simple inheritance with the same classes as in the previous post. More difficult scenarios are possible but We should watch out with inheritance because it can get to complex very fast. Try to avoid complex inheritance trees.

## Inheritance

```
class Plant

  attr_accessor :name

end

class Animal &lt; Plant

end
```
Yes Animals inherit from Plants because they share the same property so there.

And still running the same test.

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
    my_animal.name = "test"
    my_array = [my_animal,my_plant]
    my_array.each { |x| assert_equal("test",x.name,"The name is not test")}
  end
end```
Which still turns green 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/ruby14.png?mtime=1311511252"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/ruby14.png?mtime=1311511252" width="1035" height="762" /></a>
</div>

and red like before.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/ruby15.png?mtime=1311511469"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/ruby15.png?mtime=1311511469" width="1035" height="841" /></a>
</div>

## Conclusion

Simple inheritance is not that difficult, but you can easily make it a lot more difficult. I think I have now the basis to start reading code others wrote and learn more from that.

 [1]: /index.php/WebDev/ServerProgramming/RubyonRails/learning-ruby-on-windows-step-3