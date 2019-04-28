---
title: 'Learning Ruby on windows: step 0'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-23T06:09:00+00:00
ID: 1266
excerpt: |
  Introduction
  
  I guess it is time for me to learn Ruby and see what all the fuss is about. Here is how I got it installed and how I got a first little scribble done. 
  
  The IDE
  
  First thing to do is look for an IDE since those things always make you&hellip;
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows/
views:
  - 3857
categories:
  - Ruby-on-Rails
tags:
  - ruby
  - rubymine

---
## Introduction

I guess it is time for me to learn Ruby and see what all the fuss is about. Here is how I got it installed and how I got a first little scribble done. 

## The IDE

First thing to do is look for an IDE since those things always make your life easier, or maybe not. But at least it&#8217;s a quick way to get going, or maybe not. I went with [Rubymine][1] because it came recommended on [this stackoverflow question][2]. And then I read the quickstart guide. 

> Before You Start…
> 
> Make sure a Ruby interpreter is downloaded and installed on your computer. RubyMine supports Ruby versions 1.8.6 to 1.9.x. Please follow these download and installation instructions. Note that depending on your platform, you can use IronRuby, MacRuby, MRI Ruby, or JRuby.
      
> You will also need RubyGems to extend functionality of your applications.
      
> If you are going to use RubyMine for Rails development, download and install Rails framework versions 1.2 to 3.1. Note that Rails 3.x requires Ruby SDK 1.8.7 or higher. If the framework is missing on your computer, RubyMine will download and install it automatically. 

Oh man.

And so I installed version Ruby 1.9.2-p290 from [this page][3].

And then I downloaded rubygems-1.8.5.zip from [this page][4]. No idea wath I had to do with that but anyway.

I left the rails framework for what it is for now.

At this point I say hurray for [Webmatrix][5].

## Hello world

No sense in learning a new language without hello world. According to the [Ruby basic tutorial][6] it should go something like this. 

```ruby
#!/usr/bin/ruby
print "Hello Worldn"```
So I made a new file and called it helloworld.rb, like one does in these kinds of situations and copy pasted the code in, because copy pasting is what I do best. 

And I got this error.

> Error running helloworld: No SDK specified

I can understand that. So I went to settings as one does in these kinds of situations. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/rubymine1.png?mtime=1311407867"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/rubymine1.png?mtime=1311407867" width="1161" height="785" /></a>
</div>

And added the SDK like a pro.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/rubymine2.png?mtime=1311407876"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/rubymine2.png?mtime=1311407876" width="1161" height="785" /></a>
</div>

I clicked install gems on that page too but it downloaded nothing which was a bit of shame.
  
Then clicked run again and saw that it worked. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/rubymine3.png?mtime=1311407886"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/rubymine3.png?mtime=1311407886" width="879" height="611" /></a>
</div>

Woohoo. 

I even made an advanced Hello world. Just because I can.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/rubymine4.png?mtime=1311408272"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/rubymine4.png?mtime=1311408272" width="879" height="611" /></a>
</div>

## Conclusion

For a first time experience this was not to hard, although I would suggest doing something to make it even easier and I guess you know what that is too.
  
I guess now it&#8217;s time to see how to make this work from the commandline. After reading the basic tutorial I will move to [Ruby done the right way][7] because there always has to be a wrong way in every language.

 [1]: http://www.jetbrains.com/ruby/index.html
 [2]: http://stackoverflow.com/questions/16991/what-ruby-ide-do-you-prefer
 [3]: http://rubyinstaller.org/downloads/
 [4]: http://rubyforge.org/frs/?group_id=126
 [5]: http://www.microsoft.com/web/webmatrix/
 [6]: http://www.troubleshooters.com/codecorn/ruby/basictutorial.htm
 [7]: http://www.troubleshooters.com/codecorn/ruby/rubyrightway.htm