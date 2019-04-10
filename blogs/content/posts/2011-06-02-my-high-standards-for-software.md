---
title: My 'High Standards’ for Software Development
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-06-02T10:22:00+00:00
ID: 964
excerpt: "Follow me on twitter for a few weeks and you will inevitably see comments reflecting my 'high standards', generally in the form of frustration when something falls short. As a technical user I have little patience with software that is difficult to use, communicates poorly, or leaves my systems in an unstable state. Driving this frustration is the fact that we have been discussing these issues since well before I joined the field, yet I am still being sold software that doesn't meet even basic standards of usability and safety."
url: /index.php/itprofessionals/professionaldevelopment/my-high-standards-for-software/
views:
  - 3294
rp4wp_auto_linked:
  - 1
categories:
  - Professional Development
tags:
  - error handling
  - usability

---
<div style="float: left; margin: .5em;">
  <img src="http://tiernok.com/LTDBlog/rant.png" alt="Ranting Guy" style="height: 75px; " />
</div>

Follow me on twitter for a few weeks and you will inevitably see comments reflecting my 'high standards', generally in the form of frustration when something falls short. As a technical user I have little patience with software that is difficult to use, communicates poorly, or leaves my systems in an unstable state. Driving this frustration is the fact that we have been discussing these issues since well before I joined the field, yet I am still being sold software that doesn't meet even basic standards of usability and safety.

_Note: this has been sitting the queue for several months. It's not the product of a single instance of bad software, but rather the culmination of a long line of barely usable applications that had a questionable ability to tell me if they were actually working or not._

## Usability

Usability isn't a black and white topic. You don't have to be an Apple to provide a User Experience that seems intuitive or simply <span class="MT_under">doesn't</span> generate confusion and anxiety. Direct benefits can be found in increased customer understanding (especially critical during hand-offs), lower training costs, lower support costs, increased return business, and improved ability to catch gaps and misunderstandings (the earlier these are caught, the cheaper they are to correct). However many people stop at the surface arguments, that incorporating basic usability costs too much, takes to long, or requires an expert.

<div style="text-align: center; color: #999999;">
  <img src="http://tiernok.com/LTDBlog/ExtraFeatures.png" alt="Features are useless if it doesn't work" style="height: 75px" /><br /> And it's labeled in a dead language…
</div>

### The Costs

Many would argue that improving user experience costs time or resources without appreciable (or needed) benefit. Ensuring that the language used throughout the application is the same, buttons and icons follow a consistent pattern throughout the application, or the response time fits into the [10/1.0/0.1 model][1] does not provide a new form or feature we can show the client. 

Usability, however, does not provide value in bits, it provides overall value. Using consistent terminology that reflects the usage of the end consumer reduces the perceived complexity of the application, making it more 'intuitive', more 'obvious', and 'quick to learn'. Standing on the shoulders of common language and making it easier for your customer or consumer to give you money is not useless. The smaller the consumer market, the easier this should be, reducing customer confusion, training time, time lost to unnecessary phone calls, support costs, opportunity costs, and even reducing demo and end-user sign-off times. The end-user is familiar with the process the application is going to replace or enhance, using unfamiliar terminology, inconsistent terminology, or radically different workflows will frustrate them. Happy customers bring more business and trust, frustrated customers bring less business and distrust.

### The Time

We are still on a [fixed time contract|production critical deadline|2 day quick fix|etc] though, how can we justify the time to correct all the terminology, create a common set of buttons, and so on? 

There will be text on the screen. The act of entering the text is the same whether it is meaningful and adds value or sloppy and introduces costs. The difference is understanding what you are providing to your end user well enough that you can communicate it to them in their own vocabulary. The time spent understanding that vocabulary reduces communication costs inside the group and with the customer and user audience. 

And, personally, I think if you can't communicate to your customer what you are providing them, you may have deeper problems then determining the text you plan to use in the application. In fact, I would go so far as to say you might want to stop development right now and figure out why that gap exists before you spend more time trying to solve a problem you can't articulate.

### Can't Hire an Expert

We can't afford to hire an entire body to do the usability stuff for us. 

Except the business is already hiring people, like me and you, who are supposedly getting better at their jobs each year. Who will be in roles of greater responsibility some day. Basic usability (clear communications, common terminology, decent response times, consistent UI) is something that all developers should be learning as they progress, an investment that will pay for far longer than the latest language or technology shift.

And waiting for an outside mystic to come along and bless our applications with the glow of usability is every bit as silly as I have portrayed it (the waiting that is, not the usability consultant). At what point is it fair to expect a developer to be able to develop usable solutions with consistent terminology and interfaces? Should it take 5 years? 10 years? A special title? Have we communicated that expectation to our developers? Do we (and our excuses above) meet those expectations?

## Error Handling

And then there are errors, all the things that will go wrong and leave the end user unable to finish what they were doing.

There are two sides of error handling, the actions we take when an error occurs and the method we use to communicate it to our end user. A bad error handling process can actually lose you a sale or the patience of your user. 

### Atomicity

There is no try. The system should never be left in an unstable state. Transactions should execute to the end or rollback to the state before they started. 

I should be able to unplug the computer at any point, plug it back in, and start using the application again. Any partially completed work should be accessible for completion by the application or have been rolled back (or not committed) by the previous instance. I can make allowances for bugs, they make systems unstable by definition. Lack of atomicity is not a bug, however, it is a design and development flaw. A system that requires consistent behind-the-scenes corrections or completion of partially completed transactions is a system that is not working. 

When I run into applications that consistently leave their environments in an unusable state, extended maintenance contracts are just there to buy me time to replace the application (and company that produces it).

### Error Messages

There are a number of different levels to error messages. Using one generic error handler and message for every situation is the “we can't be bothered to do this” solution to the problem.

#### How Do I Finish?

When an error occurs, the end user needs an indication of what went wrong, whether they were to blame or it was an external influence, and what, if anything, they can do to alleviate the problem and continue on with their day. 

The end user is generally not using the application just because they love using the application. It is a tool, a means to an end, and if it doesn't function then they are going to find another way to get their task done. This could mean using another application or service, digging around behind the scenes, or simply deciding the task is not doable and the application is to blame (we can't bill that customer because the system won't let us, I'd love to give you a bonus but the system doesn't let us, etc).

A good error message provides information because that information will help the end user finish what they were trying to do.

#### Do You Know Why You Failed?

The other side of error messages is the technical perspective.

When an application doesn't provide me with a meaningful error message I am left wondering if the application even knows why it failed. I immediately call into question the stability of the application, because if it doesn't know why it failed then it is also likely it didn't leave things in a good state (returning to the atomicity point). So, like the end user above, I know I have something that I might have to clean up after, but I have no clue what went wrong and no way to request assistance. 

<div style="text-align: center; color: #c04623; margin: .5em; font-size: 1.25em;">
  “Hi, I got the error 'We are sorry, the system had a general error', what do I do next? Is my data still good?”
</div>

I have no idea what that partially complete task has done to the stability of my systems, so it's possible it has just caused problems elsewhere in my environment and I now need to waste my time trying to reproduce the error, not just to get the task done, but also to determine what else has been broken. 

Like the atomicity issue, if I continue to get poor communications from an application, the extended maintenance plan is just there to buy me the time to find a replacement.

## Setting the Bar

What should be obvious by this point is that I'm a little type A and that I expect a certain level of usability and design from applications I use (and build). When I'm ranting about an application or system, on twitter or in person, it's because I consider consistency, clear communications, and atomicity to be basic attributes of an application. Ignoring these basic levels of usability and atomicity makes an application sub-par and a developer that has consistently ignored them throughout their career is not yet what I would qualify as 'experienced'. 

A good developer delivers solutions. If it doesn't work, can't be used, or causes more work then it solves, then it isn't a solution.

 [1]: http://www.useit.com/papers/responsetime.html "Response Times: The 3 Important Limits - Jakob Nielson"