---
title: Be Mindful With Your Code
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-04-24T10:36:00+00:00
excerpt: |
  This post really started over fifteen years ago, if only I had known it then. You see, once upon a time I wrote a piece of code with nonsense variable names and, another time, a bunch of dynamic, inline SQL, and there was that time I tried to write half the program on one line. And lets not even get started on error messages....
  
  Then I got better.
url: /index.php/itprofessionals/ethicsit/be-mindful-with-your-code/
views:
  - 20700
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
  - Professional Development

---
This post really started over fifteen years ago, if only I had known it then. You see, once upon a time I wrote a piece of code with nonsense variable names and, another time, a bunch of dynamic, inline SQL, and there was that time I tried to write half the program on one line. And lets not even get started on error messages&#8230;.

Then I got better.

But it took a while.

And I&#8217;m not done.

## Code is for Other People

We don&#8217;t write code for the computer. We write code for ourselves an hour later, after we we have fallen out of the zone and have to debug it. We write code for 2 months from now on a Friday afternoon when it&#8217;s the only bug keeping us from our weekend. We write code for 2 years from now when the axe-wielding psycho of a maintenance developer hits the blame button to see who volunteered to be his newest, bestest friend. Or trophy. Probably trophy.

When we&#8217;re producing code, we have to be mindful that there will be a person consuming our code and stop focusing on just getting it out of our head. When we come back to this code in 6 months we want to be able to read it easily, be able to safely make assumptions about elements in it, understand the intent as well as the concrete implementation, and do so with the least amount of re-reading possible. 

### Be Consistent

Code standards tend to get a bad rap, especially among younger developers. They&#8217;re seen as unnecessary extra work or as limiting creativity. I remember thinking this way at one time and agree that over-prescription can be wasteful _(but c&#8217;mon, over-prescription of just about everything is bad, that doesn&#8217;t mean we can&#8217;t take something for our headaches)_.

The majority of code standards boil down to naming things consistently and being consistent in how functions and whitespace are structured. We do this so we can make quick assumptions about the code as we scan a file, identifying scope or type of variables, locating blocks of code, determining level of nesting, etc. Sure there is some extra work when we initially adopt a standard, we lose some time as we get used to new naming patterns and structures, but once we get used to the standards it becomes just the way we do things, no more. 

And as far as creativity goes, unless you&#8217;re taking part in a [code obfuscation contest][1], focusing on being creative at the code structure and variable naming level is a sign that you&#8217;ve lost sight of the bigger picture, namely that you have an entire program to be creative with. Or you&#8217;re too busy trying to [express your inner hipster][2], one of the two.

### Ugly Strings are Ugly

I&#8217;m not a fan of inline SQL. It tends to be written poorly, doesn&#8217;t get tested for syntax errors until the first time you hit that code path, can be nearly impossible to locate when you decide to change a field name in the database, and often gets written in a solid block of unformatted text.

And the same happens with varying degree in HTML, LINQ, Perl, &#8230; really anything that resembles 50 lines of content without a carriage return.

Even with guidelines for structuring our code, we need to be mindful of the blobs we produce. These blobs are difficult to read, adding to the level of frustration and time wasted when a future developer has to work with our code (_Who wrote this garbage code!? Oh, me, argh_). If the blob is truly a string, it deserves good formatting and consistent capitalization just as much as our code. In fact, I would say being consistent is even more important here, where color-coding and syntax checking tends to be unavailable.

And it&#8217;s ugly. We may have saved 30 seconds and got the job done, but it&#8217;s not worth taking pride in.

## Well, I Can See What You Were Trying To Do&#8230;

I once reverse engineered some business logic out of a chemical cooker simulation program written in the early to mid 80&#8217;s, revised 3 times by other developers, and utterly lacking in comments. There were no unit tests, there was one comment in the entire codebase (and it was wrong), and I often ran into cases where I could very clearly see that the developer was doing math, but couldn&#8217;t figure out why he had chosen to use set V<sub>T</sub> = f(V<sub>T+1</sub>) when everywhere else we were calculating V<sub>T+1</sub> = f(V<sub>T</sub>). 

Why were we projecting back in time with that one variable? Turns out someone had swapped the naming scheme for current and future state variables, what a pain that was to figure out. Probably saved them a few seconds to not correct it and a few more not to comment their intent, but it cost me two full days to back into what they were intending to do and determine it was a spelling error. Who knows what it cost maintainers in the 20 years in between.

There are two things we need to know when we look at a piece of code: what does it do and what it was it intended to do. We can determine what it does by reading the code, but there are many cases where we have to guess at what it should be doing. I&#8217;ve learned to start with the assumption that implementation and intent aren&#8217;t in agreement (all the while hoping I&#8217;m wrong).

Good naming schemes that identify intent, unit tests, function-level comments, good commit comments. These may seem unnecessary now, but 6 months from now they will be critical, whether it&#8217;s to maintain a piece of code or copy it for reuse.

## Error Messages Make Hulk Angry

No one likes getting an error message. An error message is the sign that no matter what we were doing, something has gone terribly wrong and we are now a whole lot further from being done than we thought we were. Obviously what we need in this tense moment is an incomprehensible or sarcastic message from the failing application to really put our mind at ease.

No?

How about this then. We develop some software, send it out in the world, and one day receive a call from an annoyed end user about the error they are receiving. The message simply says &#8220;Null reference error&#8221;, maybe with a brief joke about cheese or a reference to a technical manual for the definition of null reference. Because we all love those &#8220;oh crap, I don&#8217;t even know where to start&#8221; moments.

No?

Having been on both sides of this issue, I know neither is fun. In fact, I&#8217;d love to stop learning that lesson any time now. Error messages don&#8217;t have to increase our stress levels this much. Unfortunately there is a tendency to add the least amount of time for error tracking, compound that with poor error messages, then just live through the frustration when the situation is kicking our butts later. 

Better error messages aren&#8217;t that difficult, though, we just need to put more thought into them. We need to keep those two users above in mind when we&#8217;re crafting our message, and keep in mind the situation they will be in when this message matters. As the end user, we need help staying calm, information on how bad the situation is, a description of what our next steps should be, and the knowledge that everything is under control. As the developer, we need information about the root cause and all the state information we can get our hands on, to speed up detection and resolution.

It doesn&#8217;t really take longer, especially if we invest in our error reporting infrastructure. Most applications could afford to spend a week or more here, given how many hours/bug of debugging time we will end up saving in the near and long term. But more importantly, unnecessarily upsetting users and developers during an already stressful moment is a double loss that will hit us with every bug, exaggerating an already negative situation.

## There&#8217;s More

There&#8217;s more to this than consistency, clarity, explaining intent, and providing useful error messages. But it all starts with being mindful that when we code, we are not just writing something to jam through a syntax check and successfully compile. The code we write needs to be clear to the future reader and allow them to understand it with the least amount of re-reading and cross-checking. It needs to be aware that it has an audience, both future developers and end users. Good code is judged, not just on it&#8217;s performance characteristics, but more importantly on it&#8217;s ability to age gracefully, minimizing maintenance costs and the number of people it upsets along the way.

 [1]: http://blog.aerojockey.com/post/iocccsim "Plane-shaped flight simulator"
 [2]: https://github.com/twitter/bootstrap/issues/3057 "In case you missed the semicolon madness"