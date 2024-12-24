---
title: 'Learning Ruby on windows: First steps in rails using Rubymine.'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-30T07:21:00+00:00
ID: 1281
excerpt: 'In my attempt to learn Ruby and now rails - the hard way - I use Rubymine. So my next step is to install rails and get something on the screen.'
url: /index.php/webdev/serverprogramming/rubyonrails/learning-ruby-on-windows-first/
views:
  - 5538
categories:
  - Ruby-on-Rails
tags:
  - rails
  - ruby
  - rubymine

---
## Introduction

In my attempt to learn Ruby and now rails the hard way I use Rubymine. So my next step is to install rails and get something on the screen.

## Installing rails in Rubymine

First thing I did was to make a new Rails project or application as they call it. 

So I clicked the necessary buttons and got told that rails was not installed. Which made me click the &#8230; button next to where it say no rails found and it started installing rails.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails1.png?mtime=1312012971"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails1.png?mtime=1312012971" width="587" height="302" /></a>
</div>

But I&#8217;m sad to say that failed.

So I did it the hard way and I opened the Ruby command prompt and typed.

```
gem install rails```
And poof there is rails.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails2.png?mtime=1312013081"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails2.png?mtime=1312013081" width="707" height="675" /></a>
</div>

And I now get this when I create an new rails application.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails3.png?mtime=1312013161"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails3.png?mtime=1312013161" width="508" height="291" /></a>
</div>

## hello world in rubymine

So now we have our first rails application in rubymine and we are overwhelmed with this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails4.png?mtime=1312013835"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails4.png?mtime=1312013835" width="1035" height="872" /></a>
</div>

I would suggest clicking around a bit and getting a feel of the place. 

And now we go to a site that explains us how to make a [hello world app in rails 3][1].

And dude that looks so easy. But in Rubymine you can use a generate for this. Just go to Tools > Run rails generator&#8230;

Select controller from this list.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails5.png?mtime=1312014692"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails5.png?mtime=1312014692" width="331" height="343" /></a>
</div>

Call it hello. and add an action called index.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails6.png?mtime=1312014821"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails6.png?mtime=1312014821" width="407" height="421" /></a>
</div>

And now we got our first ever controller.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails7.png?mtime=1312014973"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails7.png?mtime=1312014973" width="1035" height="872" /></a>
</div>

Now we just adapt the routes.rb.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails8.png?mtime=1312015069"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails8.png?mtime=1312015069" width="1035" height="872" /></a>
</div>

and now we should click the run button.

And see the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails9.png?mtime=1312017384"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails9.png?mtime=1312017384" width="314" height="202" /></a>
</div>

But where does this text come from? Well, it comes from the file index.html.erb in our app/views/hello folder.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails15.png?mtime=1312017206"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails15.png?mtime=1312017206" width="708" height="507" /></a>
</div>

## hello world in the command line.

Now lets move to the commandline and see how this works.

First I typed 

```
rails new hello```
and was greeted with this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails10.png?mtime=1312015793"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails10.png?mtime=1312015793" width="555" height="706" /></a>
</div>

And this in explorer.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails11.png?mtime=1312015858"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails11.png?mtime=1312015858" width="615" height="476" /></a>
</div>

I then did the 

```
cd hello
```
and 

```
rails generate controller hello```<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails12.png?mtime=1312015993"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails12.png?mtime=1312015993" width="555" height="356" /></a>
</div>

Then I edited the routes.rb in the config folder. And I just kept the following.

```
Hello::Application.routes.draw do
  match ':controller(/:action(/:id(.:format)))'
end```
Then I had to create a file called index.html.erb in app/views/hello and I added a little text to it.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails13.png?mtime=1312016440"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails13.png?mtime=1312016440" width="617" height="276" /></a>
</div>

Then it was time to run our server via.

```
rails server```
With this as the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails14.png?mtime=1312016878"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ruby/rails14.png?mtime=1312016878" width="544" height="737" /></a>
</div>

## Conclusion

It&#8217;s all a bit overwhelming at first but knowing ASP.Net MVC and having worked with some of the MVC frameworks in JAVA, it seemed logical enough to get it working pretty fast.

 [1]: http://mentalized.net/journal/2010/02/05/hello_rails_3_world/