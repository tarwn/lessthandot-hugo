---
title: Unit Testing Costs Too Much
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-11-03T09:56:08+00:00
ID: 933
excerpt: "There is a long list of reasons not to use Unit Testing in your current projects. In fact I was able to make such a long list of reasons not to use automated Unit Testing that I wasn't able to use them all in my presentation for the Raleigh Code Camp this coming weekend."
url: /index.php/desktopdev/generalpurposelanguages/unit-testing-costs-too-much/
views:
  - 10888
rp4wp_auto_linked:
  - 1
categories:
  - General Purpose Languages
tags:
  - tdd
  - unit testing

---
There is a long list of reasons not to use Unit Testing in your current projects. In fact I was able to make such a long list of reasons not to use automated Unit Testing that I wasn't able to use them all in my presentation for the [Raleigh Code Camp][1] this coming weekend. 

While my presentation aims to dispel a number of these and show that unit testing can return a lot of value for low cost, I thought I would share the highlights from the “Why Not To Do It” list. 

Warning: I know, it seems a little lighter-hearted than my usual posts, maybe a little more sarcastic. I blame it on all the time staring at powerpoint this weekend.

<div style="color: #666666; text-align: center; float: left; margin: .5em; font-size: .8em;">
  <img src="http://tiernok.com/LTDBlog/iStock_000005447184Smaller.jpg" alt="It's a Picture!" /><br /> Stock from <a href="http://www.istockphoto.com/" title="Visit iStockPhoto.com">iStockPhoto.com</a>
</div>

## It's Twice as Much Code!

First you have to write all the “real” code, then you have to write all the test code, and before you know it you've spent twice as long writing the same amount of code. Who's going to pay for all that keyboard wear? 

_(Check out the follow-up link below for a lot more on this one)_

## I Don't Want To TDD/BDD/ADHD/OMGWTFBBQ!?!?

It seems like I have to learn 5 new things and start a blog just to do unit testing. Why do I have to care about 100% code coverage just to do some tests? Why am I doing it wrong to test the code after I write? And I have to be Agile to unit test? This seems like a much bigger investment of my time then I thought…

## I'm A Developer, Not QA!

I mean, we hired all these people to test our application and now you want me to do all the testing? are we duplicating effort or are we doing different types of testing? And is there really value in having two groups test? Wait a minute, these QA people have it all figured out, they're going to stop testing as soon as I take it over….how do I transfer?

## I'm Paid To Code, My Boss Told Me So

My boss measures my work based on how long I am pounding on the keyboard and churning out real code. How do I communicate the value and get buy in on writing code that isn't directly linked to a feature the customer can see? Is this going to be like that time I tried to explain fencepost errors, because that didn't go over so well…

## I'll Never Catch Them All…

I'm going to be preemptive about this. Unit testing won't catch all the bugs in my application, so I don't see the point in even taking the time to do it. I'd be better off getting another feature or three done and just fixing the bugs when we find them.

## And So On…

This wasn't the end of the list, but one or two of you may show up on Saturday and I don't want to give the whole show away. For those that are interested, I'll release the full presentation notes and slides afterward on my personal site and do a follow-up post with links.

Feel free to keep adding to my list in the comments below, lets see who can think of something I didn't already have.

Follow-ups:

  * Initial “Unit Testing Costs Too Much” post: [Unit Testing Costs Too Much][2]
  * Code camp review and links for slides: [Raleigh Code Camp Followup][3]
  * 2x Code Followup: [Unit Testing Costs Too Much &#8211; Twice The Code = Value?][4]
  * Too Many Things to Learn: [Unit Testing Costs Too Much &#8211; Too Many Things to Learn][5]

 [1]: http://codecamp.org/ "Visit the codecamp website"
 [2]: /index.php/DesktopDev/GeneralPurposeLanguages/unit-testing-costs-too-much "Check out the first post"
 [3]: /index.php/All/?p=999 "Code Camp review"
 [4]: /index.php/DesktopDev/GeneralPurposeLanguages/unit-testing-costs-too-much-twice-the-co "Read more on the 2x Code topic"
 [5]: /index.php/WebDev/ServerProgramming/unit-testing-costs-too-much-too-many-thi "Read more on the Unit Test Cost topic"