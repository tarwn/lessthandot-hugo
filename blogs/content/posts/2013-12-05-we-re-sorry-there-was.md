---
title: We're Sorry, There Was An Error and We're Clueless Monkeys
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2000-11-30T00:00:00+00:00
ID: 2142
excerpt: |
  "We're sorry, there was an error, please try again later."
  
  Translation? "We don't know what the heck just happened and we're hoping you will be distracted by trying again instead of wondering why we don't know what happened."
  
  Error messages area p&hellip;
draft: true
url: /?p=2142
categories:
  - IT Service Management

---
"There was an error, we're sorry for the inconvenience. Please try again later."

Translation? "We don't know what the heck just happened and we're hoping you will be distracted by trying again instead of wondering why we don't know what happened."

Error messages area pet peeve of mine. There seems to be a lot of developers out there that think this is not only an ok response, but a good one.

Because when I lose several hours of work and my calm, seeing "Please try again" or "Contact support if you need help" is definitely going to make it all better. I mean it's just an inconvenience, right? Right? 

(hint: the answer to this question indicates the value you place on your customers time).

## What's The Point

Let's forget about the message for a minute and focus on the point of it all for a moment.

Your software has blown up. The user only cares about your software because it's enabling them to get some work done or providing entertainment or otherwise allowing them to do something that has absolutely nothing to do with enjoying the actual software. The tool has just blown up in their hands and whatever time they invested or value they were expecting has just gone out the window.

In programmer terms, this is Visual Studio/Eclipse/etc crashing with a dozen unsaved files. A corrupt git repository with no backups. The RAID controller of your business database committing suicide 20 minutes before someone realizes the backups were also on the local drive. HR getting the math wrong when they determine how many people in your group to 'right-size'. Yeah.

"There was an error, we're sorry for the inconvenience. Please try again later."

Still a good error?

## Own Your Errors

Instead of focusing on blame-shifting in every direction but yourself with a 'professional' bland error message, own the damn error. Every error message should tell the user exactly what the situation is and what to do next. Even assurance of the worst case you can think of is probably not going to outdo what their imaginations will come up with if you tell them nothing at all (as they pound that retry button and get more and more angry).

Is their work recoverable? Tell them. 

What do they do next? Tell them.

Is it hopeless? Tell them.

"We're sorry, a system error occurred while saving your work and it has been lost. One of our team members will contact you in the next 15 minutes to personally help find the problem, fix it, and replace as much of the work as we can."

Yeah, that sucks. Guess what the first thing is that I'm going to do after publishing that code. Add a function to save their damn work.

Why didn't I do that 6 months ago? Oh right, there was a retry button to distract them.

## But Management Won't Let Us

Management likes the generic "distract the user from the fact that we charged them for somethign that is broken in ways we don't understand" error message? Awesome, they won't us adding a link to the corporate "About Us" and "Executive Profiles" page with an invitation for feedback. And they'll love it when they call for internal support and you greet them with "We're sorry, please try again" and hang up.

I am management, by the way. Not all of us are trying to ship broken software and charge for the illusion of working. 

## Own Your Errors

It's important enough to end with. When you feel the urge to slap a generic "We're sorry try again" message on an error, take a second look at it. Are you really going to give up and say you don't know why the error happened? Maybe a few minutes of work cuold identify the 90% case? Maybe it's going to be so infrequent that you are willing to put your name and extension on that message? Maybe you can serialize the state into a file and tell the user to send it to you? Maybe you can do all of these thing automatically, push the data into a ticket system, and email the user within a few seconds to sday that not only are you on it but you haven't lost any of their work and your not only going to fix it, but fix it so well it never hits them again.

Or, you know, you could go with the bland "please try again or contact support, we're clueless monkeys" message.