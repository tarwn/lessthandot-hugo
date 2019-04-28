---
title: Asking the right question
author: Christiaan Baes (chrissie1)
type: post
date: 2009-03-21T07:15:27+00:00
ID: 357
excerpt: "A while ago (asked Sep 18 at 14:34) I asked a question on StackOverflow. I'm pretty sure I could have found the answer via Google myself but it is so much eaasier to ask someone else to do the work for me and perhaps come up with the better answer.&hellip;"
url: /index.php/architect/designingsoftware/asking-the-right-question/
views:
  - 4221
categories:
  - Designing Software
  - Introduction to Architecture and Design
tags:
  - question
  - stackoverflow

---
A while ago (asked Sep 18 at 14:34) I asked [a question][1] on [StackOverflow][2]. I&#8217;m pretty sure I could have found the answer via Google myself but it is so much eaasier to ask someone else to do the work for me and perhaps come up with the better answer. 

The question was.

> <span class="MT_blue">Why can’t string be mutable in java and .net</span>

> <span class="MT_green">Why is it that they decided to make string immutable in Java and .NET (and some other languages)? Why didn&#8217;t they make it mutable?</span>

As we can see there seems to be some disagreement over the correct answer to that question.

Some say that security is one of the reasons.

> <span class="MT_red">First &#8211; security</span> <http://www.javafaq.nu/java-article1060.html>
> 
> The main reason why String made immutable was security. Look at this example: We have a file open method with login check. We pass a String to this method to process authentication which is necessary before the call will be passed to OS. If String was mutable it was possible somehow to modify its content after the authentication check before OS gets request from program then it is possible to request any file. So if you have a right to open text file in user directory but then on the fly when somehow you manage to change the file name you can request to open &#8220;passwd&#8221; file or any other. Then a file can be modified and it will be possible to login directly to OS.

But then others come up saying that is completely wrong.

> <span class="MT_red">You really have no business answering technical questions related to software development if you believe that immutability has anthing to do with security.</span>
> 
> I am reminded of my highschool C++ teacher who instructed us that private members were for for the explicit purpose of securing &#8216;secret&#8217; data like passwords.
> 
> Strings are simply read-only by the limitations of their implementation within the VM &#8212; they aren&#8217;t hackproof, encyrpted, or actively monitored against changes for security purposes.
> 
> Quite simply, the immutablility of string stems from the tradeoffs made for sake of efficienty (string pooling) and thread saftey. 

Now as the asker of this question should I be confused? No, not really I should learn and draw my own conclussions. I can&#8217;t really tell which of the answers is correct. I can only weigh the explanations given. And then decide which one is correct. It is even possible that both are correct. 

I might conclude that the one stating that it has nothing to do with security is wrong because he doesn&#8217;t clearly say why.

And another thing.

> did have the same thought, but checked the orginal posters location and found that they are from Belgium. Given that this means they are not likely to be a native English speaker. Coupled with the fact that most natives have a loose grasp of the language, I decided to cut <span class="MT_red">her</span> some slack. – belugabob (22 hours ago)

I might be from Belgium (who knows) but I&#8217;m definetly not a girl. I like the way the commenter did his research but then he also has to realize that there&#8217;s a difference of culture between us Belgians and the rest of the world.

 [1]: http://stackoverflow.com/questions/93091/why-cant-string-be-mutable-in-java-and-net
 [2]: http://stackoverflow.com