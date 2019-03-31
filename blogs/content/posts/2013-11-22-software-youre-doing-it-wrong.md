---
title: Software, You’re Doing it Wrong
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-11-22T14:10:00+00:00
excerpt: |
  I spend most of my waking day on a computer, using software. Then I get on twitter and share all the many ways that software annoys me, frustrates me, or just plain pisses me off. 
  
  Because most software sucks.
url: /index.php/itprofessionals/ethicsit/software-youre-doing-it-wrong/
views:
  - 24134
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
  - Other
tags:
  - software development

---
I spend most of my waking day on a computer, using software. Then I get on twitter and share all the many ways that software annoys me, frustrates me, or just plain pisses me off. 

Because most software sucks.

Most software seems to have lost sight of what it is trying to achieve or has chosen mediocrity in order to kick something out the door and label the work &#8220;done&#8221;. Unfortunately this is the kind of &#8220;done&#8221; that ends in contract and scope arguments, with overtime paid to the developers and a customer unable to do critical parts of their business. That kind of &#8220;done&#8221;.

The purpose of software is to do work for systems or people. 

## Do Work for People

Software that is built for people needs to work for people. Far to often software is built with a focus on a dry technical spec or imagined user, but the real user one day putting hands on the software and using it is left completely out of the equation. Rather than treating it as a future extension of the users hand, we get all the functionality in and then attempt to bend the user to fit the software through training or attempting to add usability as magic icing after the software is done.

<div style="background-color: #eeeeee; padding: .5em;">
  When the iPod came on the market, there was already a number of competing MP3 players, it wasn&#8217;t a groundbreaking piece of technology. What set the iPod apart was it&#8217;s ease of use. It showed us how easy it could be to transfer a CD to our music player, to have a whole music store at our fingertips and ready to be transferred into our device, and that a highly technical device could have easy to touch and use controls. What set the iPod apart was the focus on making things as easy and obvious as possible for the user, rather than the competitions focus on making a product with a complex instruction sheet, non-integrated software experience, and so on.
</div>

It shouldn&#8217;t be surprising that when you build a piece of software that is technically marvelous and considers the user only as an afterthought, someone like me will get on twitter and tell you your product sucks to use. No matter how wondrous it is behind the scenes, if I can&#8217;t understand the instructions, if it requires weeks of training with every release, if I am the least bit nervous about which button to chose to save my work, then your software does not work for me.

The purpose of software is to do work for systems and people. If I can&#8217;t figure out how to make it do work, it sucks, regardless of the internals.

## Do Work for Systems

We&#8217;re living in an age of APIs and SDKs. It&#8217;s the world envisioned by the early adopters of SOAP 15 years ago, when every company or service would have external APIs we could consume. It&#8217;s a world that started when the first programmer sat down to write a program that interacted with another program. A lot of this world sucks too.

You might be surprised to learn that the intended end user of an API or SDK is not the system. It&#8217;s a developer. And just like the previous case, the API or SDK should be designed to help the developer get work done. Except that, once again, the technical implementation often ends up being the focus, with the external interfaces just being nailed on any old way. 

<div style="background-color: #eeeeee; padding: .5em;">
  I work regularly with the Microsoft Azure API and the .Net Azure SDK. They are both well documented and have numerous examples posted. I&#8217;ve also built my own SDK to consume the API, as well as some one-off chunks of code that interact directly with the API. When I have a choice, I use my own SDK over Microsoft&#8217;s. This is not because I&#8217;m smarter or more technically proficient (I&#8217;m not), but because my SDK is easier to use, provides better feedback to the rest of the application, is consistent with the API and API documentation, and in many cases requires exactly one method call for each action I want to do against the API.</p> 
  
  <p>
    The Microsoft SDK has clumsier interfaces, requires a second set of instructions, requires extra abstraction work to allow me to write unit tests for my own code, &#8230; it&#8217;s the generic MP3 player before iPods ate their cake.
  </p>
</div>

Systems software is typically considered to be easier and free of all that people stuff. Except it really isn&#8217;t, it just has a different set of users. The ability for systems software to do work is still very much reliant on the ability for developers to use that bit of software from their future bit of software.

## Do Work

But at least we have the technical, people-free portion of the work handled, right? 

That depends on your definition of actually &#8220;working&#8221;, which has two key issues: honesty and understanding.

### Understanding the Work

Too many companies draw up a scope of exactly what the customer says they need, then methodically try to deliver exactly that scope. Some waterfall, some iterate, and at the end of the contract they beat their customers into submission with that scope. Because that&#8217;s the easy way to do it.

<div style="background-color: #eeeeee; padding: .5em;">
  I once worked on a hugely overdue project to deliver a piece of line-of-business software to a company that we hoped would become an ongoing revenue stream. We had a high level scope and someone had pulled some deadlines out of thin air, it was roughly 9 months into a one year deadline and the customer hadn&#8217;t seen the software yet. All of it was designed and built from weekly phone calls and that initial scope document, with prototyped sample images that we later found out had a high level of artistic license and low level of accuracy. The customer had some very real pain points, but we had a document that explained exactly what we were going to deliver. </p> 
  
  <p>
    Relations were strained, the customer had gone out on a limb for this project and we kept delivering badly interpreted versions of what he was asking for after not showing him anything for months at a time but an envelope with a bill in it.
  </p>
  
  <p>
    The software sucked. And not just because of all the quality problems, performance problems, build problems, integration problems, usability problems, and things we created that they never asked for. It sucked because it was so focused on solving an abstracted, prototyped, guestimated version of the customers process that it totally overlooked the work that needed to be done as well as the genuine pain points that we could have focused on in far less time for far greater effect.
  </p>
  
  <p>
    It didn&#8217;t work and it didn&#8217;t solve the right problems.
  </p>
</div>

The purpose of software is to do work, if it does the wrong work, then no matter how technically sound or brilliant, it sucks. And if you missed the purpose, you probably didn&#8217;t make it very easy to use either, so good luck with that.

### Honest Work

Down to the wire, the software has to be delivered next month, and there&#8217;s still 3 outstanding features and a slew of bugs. We&#8217;ll just ship what we have and if they have any problems with it we&#8217;ll have the lawyers point out that we technically delivered what the spec said we would. Point out where it says we can&#8217;t charge you to fix the bugs preventing it from running.

What crap.

<div style="background-color: #eeeeee; padding: .5em;">
  That project I mentioned above? They chose the features because we told the customer it would have X, Y,and Z, so we have to ship X, Y, and Z. We&#8217;ll ignore the fact that the save button on X takes over 30 seconds and occasionally mangles the data, that the page for Y only works for half the users, and prior features like A, B, and C still have outstanding issues from months before. We shipped it, it must be done.
</div>

We are delivering tools to do a job, if it doesn&#8217;t work then it doesn&#8217;t work. Delivering it that way and hoping that the customer won&#8217;t notice? Fraud.

I&#8217;m not saying every piece of software has to be delivered bug free, what I&#8217;m saying is to be honest about your work. And in the case above, honesty means owning up to the customer that you will not be able to deliver what you promised and now have to chose between fixing these 3 broken features or adding 3 more. Which is painful, I&#8217;ve had to have that conversation before, but lying by delivering a mass of unworking software is for charlatans, not professionals.

The purpose of software is to do work, if it doesn&#8217;t function then it doesn&#8217;t do the work.

## It&#8217;s About People

At the end of the day, software is about people and too much software forgets that. Even the code we write should be written for future developers to read and improve upon, covered in unit tests to provide a safety net for future development, revised and improved upon as we gain a better understanding of the goals of the work the user wants to do or as we find better ways for them to interact with the software. And I haven&#8217;t even mentioned all the non-code aspects of good software, like the importance of good support and communications.

The purpose of software is to do work for systems and people. For people and by people. Miss that and random tweets are the least of your worries, it won&#8217;t take the next iPod to eat your lunch, just a mediocre application that gets some of that equation right.