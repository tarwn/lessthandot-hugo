---
title: Learning Elixir and Phoenix – Getting a handle on the language
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-03-23T12:18:20+00:00
url: /index.php/webdev/learning-elixir-and-phoenix-getting-a-handle-on-the-language/
views:
  - 2206
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - erlang elixir phoenix

---
Recently I started learning Elixir and the Phoenix framework. I&#8217;ve found that mixing hands on programming with reading is the fastest way for me to get up to speed with a new language or framework, so if that&#8217;s your style you might find this useful. In this post I started at zero and ended at being able to write small modules, run unit tests, and work with the interactive console.

Previous Post: [Learning Elixir and Phoenix &#8211; Environments and Editors][1]

<div style="background-color: #FFFFCC; padding: 1em; margin: .5em; border: 1px solid #EEEEBB; border-left-width: 16px;">
  Note: I&#8217;m just getting started, so this is not an expert post on the best way to learn Elixir, but closer to a log of the most useful things I used along the way. As I gain experience I&#8217;ll likely have to revisit earlier assumptions and correct myself.
</div>

As I stated before, my goal is to get from “barely able to read it” to “able to ship readable, idiomatic, testable apps”. This is the path I took, including problems and successes I ran into along the way. The links I include are probably less than 5% of what I actually read and tried, this is the distilled set I think helped me the most.

# Getting Started &#8211; the interactive console

My first step was walking through this [Getting Started with Elixir][2] post. It provided some background on Elixir, then walked through the first few commands in the interactive console. It may seem silly, but I duplicated every example in the console myself, trying to start building a little muscle memory on some of the basics.

At this point I didn&#8217;t really know what mix was (think npm for node or nuget for .Net PLUS scaffolding commands from yeoman or .Net Core cli) or why some files have .exs extensions and some only .ex (covered in one of the later resources).

# Learning Syntax

Next I walked through [Elixir School][3], working side by side with the examples from the [Basics section][3] through [Modules][4]. In each case, I would try the posted code example, then try 1-2 alternatives. My intent was, again, to try and exercise the syntax and start building a little more understanding of the language.

Here&#8217;s some of the notes I made along the way:

  * String concatenation is weird <>
  * &#8216;Arity&#8217; -> number of arguments for a function
  * Enum is the underscore of Elixir
  * Case default uses the _ to match the final arg &#8211; &#8220;_&#8221; is a variable and it just happens to match anything that made it this far &#8211; Elixir doesn&#8217;t provide warnings for not using variables that belong with underscore

So very much trying to fit what I&#8217;m learning from Elixir with what I expect and know from other languages.

As I got to &#8220;with&#8221; and functions, I noticed I was starting to psych myself out. I know a lot of languages and have pushed a lot of code to production, but there&#8217;s this voice in the back of my head that says this is just too different from what I&#8217;m used to working with. That shipping something meaningful is incredibly far away and that I may never be able to understand this language well enough to write it in an idiomatic way.

So I took a break and then came back to it ([Decision fatigue][5]?). I was trying to climb a pretty steep learning curve and sometimes the best thing I could do was spend some time away and let the back of my brain digest for a bit.

# Creating a project

Elixir runs on top of Erlang, which is designed to be a distributed, resilient system. The next stop I made was [How I Start &#8211; Elixir][6], written by the creator of Elixir, [José Valim][7].

In retrospect, I wasn&#8217;t quite ready for this, but it got me in and writing some code (which was good) and I got some of the basics he was communicating, even if some of the deeper detail didn&#8217;t sink in.

Because this was the most project-y exercise so far, this is also where I started branching out and trying [various editors (previous post)][1]. I also ran into issues getting my vagrant host talking to my a running node on my Surface tablet, which some brief searching suggested was probably firewall issues that I didn&#8217;t have the energy to dig into (because who really wants to fart around with debugging firewall issues on Windows in their free time).

# Even more hands on

To get even more hands on, I purchased the [Take Off with Elixir][8] ebook + videos. This promised a fun learning experience building a system, which felt like exactly what I was looking for.

There is a 30 minute preview [on the page above][8]. It is worth the time even if you don&#8217;t purchase the video. It helped provide examples of implementation versus idiomatic implementation, filled in some gaps around types, and generally expanded my knowledge in a number of ways.

So I purchased the longer form version and started working along with it. 

I had a little confusion about whether I should be starting with the ebook or the videos, so I just started with the video. I rarely pulled down his github examples unless they were necessary to finish an exercise (&#8220;I wrote some additional tests, debug them&#8221;), instead opting to hand write the code as he did, pausing the video if I needed more time. This continued to help reinforce what I was watching and forced me into a couple useful sidetracks, like making the Atom plugins work, identifying an out of date package on one Windows system, and digging deeper in a case where I thought I had solved an equation right but had some minor syntax issues that my new elixir eyes weren&#8217;t seeing.

There were a few cases where I didn&#8217;t quite know what he wanted (&#8220;write some more tests that you think would be useful&#8221;, &#8220;round the number 19&#8221;), so I spent some time doing something similar then moved on. 

By the time I made it through chapter 14, I started to get sidetracked again, so this was as far as I made it.

# What&#8217;s Next

The next two things I&#8217;m doing are writing a small CRUD application, which pushed me into actually doing some Phoenix and Ecto/Postgres stuff, and working through [The Little Elixir & OTP Guidebook][9]. As I wrap these up I&#8217;ll provide a refined list of resources and lessons that helped the most and how some of the simplest things (like calling an API) managed to sidetrack me the most.

 [1]: /index.php/webdev/learning-elixir-and-phoenix-environments-and-editors/
 [2]: http://culttt.com/2016/02/29/getting-started-with-elixir/
 [3]: http://elixirschool.com/lessons/basics/basics/
 [4]: http://elixirschool.com/lessons/basics/modules/
 [5]: https://www.fastcompany.com/3009641/quick-end-decision-fatigue-before-it-drains-your-productivity-reservoir
 [6]: http://howistart.org/posts/elixir/1/
 [7]: https://github.com/josevalim "José Valim on github"
 [8]: https://bigmachine.io/products/take-off-with-elixir/
 [9]: https://www.manning.com/books/the-little-elixir-and-otp-guidebook