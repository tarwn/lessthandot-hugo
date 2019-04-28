---
title: 'Learning Ruby on windows: First steps in rails scaffolding'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-30T12:09:00+00:00
ID: 1283
excerpt: So this morning I started with rails and created my first application, of course this had to be an hello world type of application. Now I would like to make something a bit more challenging. A plant database with a name, the height and the color.
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows-first-1/
views:
  - 11974
categories:
  - Ruby-on-Rails
tags:
  - rails
  - ruby
  - rubymine
  - scaffold

---
## Introduction

So this morning I started with rails and [created my first application][1], of course this had to be an hello world type of application. Now I would like to make something a bit more challenging. A plant database with a name, the height and the color of that plant. I said a bit challenging not very challenging. But it was challenging enough.

## Scaffold

I would recommend reading [this guide][2] to get more of the details.
  
I decided to use scaffold to make this quick and easy. And in rubymine you do this via Tools > Run rails generator&#8230; and then you fill in the model like the below.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/scaffold1.png?mtime=1312033743"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/scaffold1.png?mtime=1312033743" width="355" height="308" /></a>
</div>

which then creates a model, controllers, helpers, views, a database script, &#8230;

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/scaffold2.png?mtime=1312033881"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/scaffold2.png?mtime=1312033881" width="916" height="824" /></a>
</div>

And then it is time to run our application and see if it works.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/scaffold3.png?mtime=1312033959"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/scaffold3.png?mtime=1312033959" width="732" height="526" /></a>
</div>

And that wasn&#8217;t the desired result.

For some reason my table was not created. So I did a rake db:migrate from the commandline and found there was an error with my rake version and found that I was not the only one with this problem [on stackoverflow][3].

Here is the error.

> rake aborted!
  
> uninitialized constant Rake::DSL

and yes a gem install rake -v=0.9.2 and a bundle update later I could do a rake db:migrate and see this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/scaffold4.png?mtime=1312034237"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/scaffold4.png?mtime=1312034237" width="699" height="216" /></a>
</div>

There is nothing that makes a man more happy then seeing that it works.

So back to running the server and going to my site and the route in this case is plants not plant.

and I can add a plant.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/scaffold5.png?mtime=1312034363"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/scaffold5.png?mtime=1312034363" width="219" height="137" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/scaffold6.png?mtime=1312034517"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/scaffold6.png?mtime=1312034517" width="224" height="273" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/scaffold7.png?mtime=1312034530"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/scaffold7.png?mtime=1312034530" width="232" height="167" /></a>
</div>

And then the list looks like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ruby/scaffold8.png?mtime=1312034539"><img alt="" src="/wp-content/uploads/users/chrissie1/ruby/scaffold8.png?mtime=1312034539" width="302" height="149" /></a>
</div>

## Conclusion

If everything works it is pretty simple to make something new. This can be used to make something simple really fast, no idea how it will work if you want something a bit more difficult. What you would certainly use this for is to look at the code it produces and what you can learn from that.

 [1]: /index.php/WebDev/ServerProgramming/RubyonRails/learning-ruby-on-windows-first
 [2]: http://guides.rubyonrails.org/getting_started.html
 [3]: http://stackoverflow.com/questions/6085610/rails-rake-problems-uninitialized-constant-rakedsl