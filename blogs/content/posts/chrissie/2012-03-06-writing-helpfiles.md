---
title: Writing helpfiles
author: Christiaan Baes (chrissie1)
type: post
date: 2012-03-06T05:40:00+00:00
ID: 1547
excerpt: About writing docs/help files and what programs I found that are reasonable and cheap.
url: /index.php/itprofessionals/itprocesses/writing-helpfiles/
views:
  - 1834
categories:
  - IT Processes
tags:
  - helpinator
  - helpndo

---
A man&#8217;s gotta do what a man&#8217;s gotta do, and the last few days it has been writing helpfiles. Or actually I&#8217;m writing a manual for my application(s). And why would I need to do that? It&#8217;s not like my users don&#8217;t know how to use my program they have been using it for years in one form or the other. Or that&#8217;s what I think anyway. 

The problem is that we are going for accreditation and everything has to be documented so that new people can more easily get to grips with what we are doing and so that the older people can prove that what they are doing is what everyone else on the team is doing. So that&#8217;s why you need a manual/help file/documentation. This is multu important because we want all persons in the organisation to work the same way and deliver a quality service to the client, so that the client knows that he will be getting the same service all the time or better (because we aim to improve). 

So apart from validating your software and checking it does what it should do when you think it should do. You also need to write out what is is supposed to do and how people should do this. 

The checking part we can leave to automated testing in multiple forms. But the describing part is left to someone with good knowledge of the program and writing skills. I have neither but I did it anyway.

So I started with looking how best to do this. I wanted to add this documentation so it was easily found in the application and a way to search through and a way to have it all in one PDF for QA reasons. 

For me chm is still one of those formats that do what I want but they have been deprecated for centuries now. I like chm because it let&#8217;s you compile everything into one file as opposed to HTML where you will end up with a gazillion files. But HTML would do just as well. 

Another problem was the language. Living in Belgium everything needs to be either bilingual (French and Flemish) or just plain English. Because my time is limited I choose to go for the English only option.

I found 2 programs that I like that can do all of the above and a little more. And that are not to expensive.

[HelpNdoc][1] and [Helpinator][2].

Both do pretty much the same thing. You make a helpfile and then you can compile it into different formats you need. Although helpinator does not make word documents. The price is pretty much the same. Helpinator has otherwise a lot more options and makes it easy to do multilanguage docs. But I picked helpNdoc because it is simpler. 

There are of course lots of other more expensive solutions. 

Would be nice to know how other people solve this in the .Net world, since creating help files/documentation seems to always be an afterthought and not even part of many blogposts. I guess it&#8217;s a bit like globalization and localization. But to me both things are first class citizens of any application that is used by normal people.

 [1]: http://www.helpndoc.com/
 [2]: http://www.helpinator.com/