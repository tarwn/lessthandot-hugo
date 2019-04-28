---
title: My very first pull request
author: Christiaan Baes (chrissie1)
type: post
date: 2012-07-22T06:02:00+00:00
ID: 1678
excerpt: My very first pull request and it involves easyhttp.
url: /index.php/itprofessionals/professionaldevelopment/my-very-first-pull-request/
views:
  - 1660
categories:
  - Professional Development
tags:
  - easyhttp
  - github

---
## Introduction

I am a one man team of developers. This has the advantage that I work with the most awesome developers in the world. But this also means it is kind of difficult to learn new things. For this of course the internet is a great place. 

Blogging is a great way to learn and a great egoboost from time to time or a humbling experience when you get it wrong. And it is always good to read other peoples blogs. Most people however will limit themselves to simple examples to get the point across, which is understandable.

And then you have twitter. But twitter is limited in characters. So then comes opensource. 

Reading someone else&#8217;s code can be a good way to learn but you have to be sure to not learn the wrong thing. Of course you can learn from someone else&#8217;s mistakes too. And there is plenty of code to read on Github, codeplex and Google code. More code than you can ever read in one lifetime. 

And then the last step is to actually contribute something. I kinda think I already contributed to some projects by submitting bugreports. But there is nothing more satisfying than making the changes yourself. 

## The story

So last weekend I was writing a blogpost about servicestack and easyhttp and I thought the easyhttp needed a way to add the queryparameters in a better way than me having to concatenate the string myself. So I discussed this with Hadi Hariri, the creator of easyhttp. And he told me he would be glad to accept my pull request. I for one thought it was going to be a simple change. 

So I forked the code. And made the change. Simples! No not really. For one easyhttp uses mspec and for some reason I could not get the resharper test runner to run those tests. So I gave up on that and wrote some nunit tests instead and Hadi would convert them when he had the time.

Fast forward a week. Hadi didn&#8217;t have time to write the mspec files and had therefor not yet granted me my wish, uh pull request. So I set about trying to run the tests and convert them myself. I discovered that ncrunch ran the tests. I had not yet used ncrunch in anger, but I can tell you it works. Of course some of them did not pass on my machine. Because I did not have couchdb installed. So I installed it and made a database. Another new thing I learned.

So I converted the nunit tests to mspec and made everything pass. And told Hadi about this. Hadi then rejected my first pul request and I made a new one. 

We skyped a little about the code and Hadi thought it would be nice if also the post, put, stash,&#8230; So he rejected my pull request again. And I set about making the changes and added more tests. And then I made another pull request. And this time it was accepted. And built into a brand new version of easyhttp. Namely 1.5.3.

## Conclusion

I learned at least 4 new things while doing this. So what more reasons do you need to contribute to an opensource project? Oh yeah you might want to look for someone as awesome and patient as Hadi for your first pull request.