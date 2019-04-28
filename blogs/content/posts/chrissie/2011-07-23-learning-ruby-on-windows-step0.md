---
title: 'Learning Ruby on windows: Step 0.1 testing the hello world'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-23T07:26:00+00:00
ID: 1267
excerpt: |
  Introduction
  
  I am learning myself Ruby not because I need to but just because I can. In my first post I showed you my setup that I will be using for now. And I got hello world working. 
  Now I want to write a test to see that my output is what it is&hellip;
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows-step0/
views:
  - 1715
categories:
  - Ruby-on-Rails

---
## Introduction

I am learning myself Ruby not because I need to but just because I can. In [my first post][1] I showed you my setup that I will be using for now. And I got hello world working.
  
Now I want to write a test to see that my output is what it is supposed to be.

## The problems

In encountered a few problems when trying to make my test work. First was how to capture the stdoutput. This was fairly easy, namely Google. I found this [thinkingdigitaly][2] post very useful.

And I used this little piece of code to make it work for me.

```ruby
require 'stringio'

module Kernel

  def capture_stdout
    out = StringIO.new
    $stdout = out
    yield
    return out
  ensure
    $stdout = STDOUT
  end

end```
Which made me look for a way to include this file in my tests because I wanted to keep this separate from my testcode.
  
It seems that you need to use require_relative &#8220;nameoffile&#8221; to do that.

I then needed a function in my helloworld code so I could invoke it and test it.

So this became.

```ruby
#!/usr/bin/ruby
def helloworld
puts "Hello world advanced"
end```
def seems to be the way to go.

## The test

And now it is time to see test. I used TestUnit for this and not rspec. 

```ruby
require "test/unit"
require_relative "module"
require_relative "helloworld"

class MyTest &lt; Test::Unit::TestCase

    def test_if_hello_word_advanced_is_put_on_stdout
    out = capture_stdout do
      helloworld
    end

    assert_equal "Hello world advancedn", out.string
  end
end```
Tests just begin with the word test and the class inherits from a bunch of stuff. and the assert_equal seems to be obvious. 

When I run the above I get this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/rubymine5.png?mtime=1311412915"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/rubymine5.png?mtime=1311412915" width="1035" height="762" /></a>
</div>

I have to figure out this gems thing pretty soon me thinks. And then get rubymine to play nice with the test framework.

## Conclusion

Google to the rescue. But in the end it went pretty smoothly. At least I got my first test running. Test are very important in any language so they are the best place to start learning.

 [1]: /index.php/All/
 [2]: http://thinkingdigitally.com/archive/capturing-output-from-puts-in-ruby/