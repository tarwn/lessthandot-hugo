---
title: AngularJS vs Knockout - Final Thoughts (9 of 9)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-21T13:38:00+00:00
ID: 2182
excerpt: "I started reviewing AngularJS and Knockout because I had some specific projects that I intended to use one of these for and felt the research and comparative examples might prove useful to others. I haven't compared every aspect of the libraries, just enough to give me an idea which will be better for my specific projects (and hopefully give you a headstart on your own decisions)."
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-final-thoughts-9/
featured_image: /wp-content/uploads/2013/10/Javascript2.png
views:
  - 98035
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - angularjs
  - knockout

---
I started reviewing [AngularJS][1] and [Knockout][2] because I had some specific projects that I intended to use one of these for and felt the research and comparative examples might prove useful to others. I haven't compared every aspect of the libraries, just enough to give me an idea which will be better for my specific projects (and hopefully give you a head start on your own decisions).

<div style="background-color: #eeeeee; padding: 1em;">
  This is the final post in a nine post series looking at the capabilities of knockout and Angular. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1" title="AngularJS vs Knockout - Introduction">introduction post</a>, I outlined the capabilities that I am evaluating for and the variety of scripts used in the series. In each post I evaluated a capability I needed and the ability to deliver that capability with both libraries, comparing their differences and offering my opinions. This post shares my final feelings.
</div>

Knockout and AngularJS are attempting to solve two different problems. One is an MVVM binding framework, the other is an MVC SPA-in-a-box solution. But having read a number of posts that perform shallow comparisons, balk at the complexity of adding additional libraries, or simply have errors in them, I thought it could be done better. So I set out to perform a deeper comparison, include extra libraries when needed, and see how they stacked up against a common set of necessary capabilities. 

This post has my final thoughts, but the previous 8 dive into individual comparisons and have more specific comparisons and thoughts along the way. If you have not done so, I urge you to at least skim through those as well (and not just for the extra hit counts).

## The Winner Is...

The winner is both of these frameworks. I'll get opinionated in a moment, but I want to point out that neither of these frameworks is the wrong decision. They are both very useful and the decision to use one over the other is not going to be because one is innately poorer than the other, it's going to be because one fits the style and specific guidelines for a project better than the other. Saying that one is better than the other in all scenarios is just laziness.

I don't like the term 'front-end developer'*, but if that's your role and you're not a junior front-end developer, then you ought to know at least a couple of overlapping (binding, structure, etc) frameworks for web applications, such as Knockout, AngularJS, Backbone, Ember, Meteor, Dojo, etc. If for no other reason than because we've had a very public reminder recently at how messy HTML/JS development can get using gobs of jQuery and global functions (healthcare.gov). 

If you're writing blog posts and telling people which one is the best one, you better know them all. And have worked on a variety of project sizes, from tiny 3-5 page/route sites to whatever huge means (100's of pages/routes?). And you need to have trained new developers how to use them, torn them out of a couple projects when they started to fall over, and refactored and kept them on a couple other projects. Because I absolutely think that there is not one framework that is globally better in all ways. It is very much situational, and projecting experience from one or two projects to all possible projects is a sign of an [advanced beginner][3] or maybe an [expert beginner][4].

Yep, if the shoe fits, I just used fancy words to call you ignorant. That's why we have a comments section.

So I won't be telling you which of these frameworks you should be using for your projects, but I absolutely have some opinions that shaped my own decisions and will shape future decisions I make regarding these frameworks.

_*front-end developer: my feelings on this term (as well as backend developer) is a whole seperate blog post._

## Let's Compare Some Stuff

It's time to make some decisions. You've read this far along in the series and hopefully that means that if one of these frameworks was new to you, you now have a starting point to explore the other and know what constructs map to one another across the two frameworks.

But how would I use them?

I approached these two frameworks as a beginner. 

There are posts out there by people more knowledgeable than me with these frameworks, at some point I'll be one of those people. There are numerous other topics I had difficulty learning and then learned well enough to take them for granted, forgetting my earlier difficulties. I often wish I had posted on them as I learned them, so I could remember and communicate how I got past those early issues.

So I am posting as a beginner, probably the last time I can compare these two frameworks on relatively similar footing before I start using them in projects and building greater cognitive biases. 

Granted, I have a few headstarts, having done professional web development for over a decade, working with all manner of MVC/MVVM/MVOMG presentation patterns plus any number of non-presentation patterns, experience in a large number of small to large-to-me (1/2 million lines?) projects, and finding a somewhat random mix of things interesting and worth playing with. I also had previously read a number of blog posts on both, watched videos on both, and used Knockout briefly for some small prototypes and the [SQLisHard.com][5] website (and learned enough that I want to rewrite it now).

So after a couple pages of text, lets get opinionated (opinionated-er?).

## Sunblock On, Fire suit applied

Before I get into the ranting, two notes:

1: I plan on using both of these frameworks for projects in the future, they are both far better than building my own

2: What makes sense as a beginner may sound like total nonsense to someone who knows every nook and cranny, tell me (nicely) why or how I'm wrong

2b: Keep in mind that it may not be me who is wrong

2c: If I'm wrong and you also had that same wrong impression at one point, help us understand how/where you learned better

And now on to the opinions.

### Composition vs All-In-One

I think the all-in-one approach of [AngularJS][1] makes it a great choice for small projects (less than 10 screens or 10-20 JavaScript files) that are using a SPA model or want to take advantage of several of AngularJS's capabilities. You have a single package to maintain with reasonable tools that can operate at higher scales and there are tons of examples available. 

When the project starts getting larger, I don't think there is a clear advantage to either. Knockout would obviously require a composition approach at any scale, meaning more packages to find, monitor, and maintain and several communities for support and continued developent instead of just the one. But from what I can see, you're going to need to start adding more overhead on the AngularJS side as well, either in the form of custom routing packages, a full DOM manipulation library like [jQuery][6], unit test libraries, 3rd party web component libraries, ... When we start talking about larger scale projects, it stops being an all-in-one vs composition conversation and is instead a flavors of composition conversation.

I am concerned that most all-in-one solutions I've worked with in the past tend to be good at a couple things and mediocre at the rest. In some cases this means you're just screwed (VB6 seemed to have a few places where the bar was pretty low, then you would try to surpass it and suddenly it was 100x harder), which leaves you either living with the mediocre bits or replacing them with better ones and just living with the overhead.

So the only time I think all-in-one vs composition helps make a decision is in the small scale or when you replace logic in the all-in-one with additional libraries and have to live with the extra file download size.

### No Compelling Capabilities

There were capabilities in the AngularJS that weren't available in Knockout w/ extra libraries, but none of them were that compelling to me. Would it be a handy tool to have? Heck yeah. Would it be impossible to work without it? Not really. 

[Transclusion][7] was the one that stood out the most for me, most of the others were comparable or available once the relevant extra library was added. Transclusion is a plus I get for working with AngularJS, but it's absence on the Knockout side doesn't suddenly make everything impossible. Transclusion (and it's lack) is not new to me, most of the server-side languages I have used did not directly support transclusion (Perl, PHP, Classic ASP, etc), in fact the only one I can think of that had it is ASP.Net Web Forms.

AngularJS and Knockout both had additional capabilities that were different than the other, but none of them leapt out at me and made the package irreplaceable.

### Documentation

I really didn't like the documentation for AngularJS. 

The documentation defaults to the latest unstable version for API methods, so you can't trust google search hits. Changing versions in the documentation version selector redirects you back to the [main docs page][8] for that version, which means then manually searching for what you were looking for. Many of the API functions had inadequate examples (one uselessly basic example of [ngClick][9] compared to 6 in [knockout][10]), so I often had to experiment with them to figure out how they worked. 

The general guide information can't seem to decide what level of developer it is addressing, often switching between beginner and advanced topics or presenting them out of order. I had lots of difficulty figuring out how to [define dependencies][11] in the beginning, which is a pretty fundamental feature of the library. The [tutorial][12] assumes you are familiar with git and setting up a web server, which makes it less than approachable if you're missing any of these. 

I found after a while I was looking for blogs on topics instead of trying to read the documentation.

On the other hand, Knockout's [documentation][13] is much more approachable, with web searches landing me on documentation that works with the current stable version and numerous examples. I rarely found myself reading and re-reading sentences in the Knockout documentation, it either does a better job of communicating the details or has fewer complex details. The [tutorial][14] required no installation at all, operating similar to jsfiddle and letting me get started directly from the web page. The site also has a number of [live examples][15] available directly in the site, from basic bindings to editable grids of varying complexity, to a twitter client that unfortunately is no longer functional (twitter API deprecation).

[RequireJS][16] also worked with with search engines and the getting started was pretty easy, but there was a bit of a gap between getting started and actually using it. I had some difficulties with shims that took me a couple iterations with the documentation to get through, but that reminded me of just about every attempt to work with AngularJS's documentation.

The various routing frameworks had differing levels of documentation, with my favorite ([finch.js][17]) posting all of their examples in CoffeeScript. But again, the complexity and requirement to reread everything in AngularJS's documentation eclipsed any issues I had with these libraries.

[Squire.js][18] had the least amount of documentation and also suffered from a limited number of blogs to draw on, if I get some time I'll probably add some blogs about this in the future just to help out a bit. I had some confusion points getting started that probably could have been resolved pretty quickly with a tutorial or interactive example. This was probably the second-most frustrating documentation scenario (after AngularJS).

### Erroring

There were a number of people that tried to set me straight on AngularJS's silent binding failures. You can read the full comments after the [binding post][19], or the slightly more prepared version here.

I am violently opposed to silent failures and errors that don't provide reasonable information. There are a number of exceptions in .Net that provide absolutely no useful information for troubleshooting (Null reference, for example). There are silverlight errors that only contain a usable description if the user has the Silverlight Development tools installed. When I used JSP a long time ago, you would only get errors from the servlets that had been generated for you, often with no relation whatsoever to the code you had actually written. 

I can't count the number of systems that I've found empty catch statements in that were silently eating errors and directly causing pain to the end user.

There are two scenarios where you will have a binding error situation, when you have attempted to bind to something that doesn't exist yet (async operation) and when you have a bug, for instance updating a model but missing something that bound to it.

Silent failures are a feature in the first case. They mean you don't have to build conditional sections in the app that evaluate the state of objects you want to bind against, hopefully leave the screen blank when they fail (I had cases in Angular where I could still see the {} statements, ick), etc. In short, they don't put an error in the console for the end user and require less developer time. Although I'm not sure how many users actually notice script errors, I guess it depends on your user base and whether a storm of developers is visiting and looking for issues (again, like healthcare.gov).

The second failure is a bigger deal. Attempting to build or maintain an application that doesn't tell you when errors happen is painful. It means that when you get these type of binding errors you have to hope that either QA catches it or that you have made it easy for your users to communicate them to you. If the bug makes it into the wild, your application looks broken and you don't even know about it.

These are also the most painful errors to fix, because not only are you relying on someone else to try and tell you how they got to the error state so you can duplicate it, you don't have any clues from the framework even when you duplicate it. Is the property mis-bound? A parent property undefined? 

That's the kind of stuff that keeps me awake at night. User's looking at a broken interface and questioning whether they are doing something wrong or if it's broken, and if it's broken whether they can trust me with their money. And I don't even know why they're churning or that I missed that opportunity to save my credibility.

In my career, I have built green field projects, but I have spent as much or more time on applications that have some years behind them. The harder you make it to find and fix errors, the more it's going to cost to develop new features and the less time you're going to have to do so.

AngularJS has silent binding failures, Knockout produces errors. Of the two, that second is the better situation because I presume I can at least grab them from window.onerror. The perfect situation would be to be able to give either of them a handler to report those errors to, but that's not a pattern I have seen with any javascript library I've worked with.

## Which am I Choosing?

One of my projects is big and is going to have a constant flow of features added, credibility and trust are very important, size of the script files are going to be very important, asynchronous script loading is going to be important, I don't plan on rebuilding it for a while ... that one should be obvious.

That being said, I have every intention of using both of these in smaller projects and even reworking the knockout code in [SQLisHard.com][20] at some point in the future. 

The more important question is, which are you choosing? What factors did I cover that were more or less important for your project? What did I miss?

<div style="background-color: #DDDDDD; padding: 8px; width: 400px;">
  <h3>
    Knockout vs AngularJS
  </h3>
  
  <ul style="margin: 0px; padding: 4px;">
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1">Introductory Post</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-data-binding-2">Data Binding</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-validation-3">Validation</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-serialization-4">Serialization</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-templating-5">Templating</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-modules-and-di-6">Modules + Dependency Injection</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-automated-testing-7">Automated Testing</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/AJAX/angularjs-vs-knockout-spa-routing-history-8">SPA Routing/History</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-final-thoughts-9">Final, Final Thoughts</a>
    </li>
  </ul>
</div>

 [1]: http://angularjs.org/
 [2]: http://knockoutjs.com/
 [3]: http://en.wikipedia.org/wiki/Dreyfus_model_of_skill_acquisition
 [4]: http://www.daedtech.com/how-developers-stop-learning-rise-of-the-expert-beginner
 [5]: /index.php/DataMgmt/DBProgramming/sql-is-hard
 [6]: http://jquery.com/
 [7]: http://docs.angularjs.org/guide/directive#understanding-transclusion-and-scopes "AngularJS: Directives - Understanding Transclusion and Scopes"
 [8]: http://code.angularjs.org/1.0.8/docs/api
 [9]: http://code.angularjs.org/1.0.8/docs/api/ng.directive:ngClick "AngularJS: ngClick"
 [10]: http://knockoutjs.com/documentation/click-binding.html "Knockout - 'click' binding"
 [11]: http://docs.angularjs.org/guide/di "AngularJS: Dependency Injection"
 [12]: http://docs.angularjs.org/tutorial "AngularJS tutorial"
 [13]: http://knockoutjs.com/documentation/introduction.html
 [14]: http://learn.knockoutjs.com/ "learn.knockoutjs.com"
 [15]: http://knockoutjs.com/examples/ "Knockout - Live Examples"
 [16]: http://requirejs.org/
 [17]: http://stoodder.github.io/finchjs/
 [18]: https://github.com/iammerrick/Squire.js/ "Squire.js on github"
 [19]: /index.php/WebDev/UIDevelopment/angularjs-vs-knockout-data-binding-2
 [20]: http://sqlishard.com