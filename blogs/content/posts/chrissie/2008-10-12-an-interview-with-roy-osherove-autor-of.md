---
title: An interview with Roy Osherove author of "The Art of Unit Testing"
author: Christiaan Baes (chrissie1)
type: post
date: 2008-10-12T13:47:07+00:00
ID: 173
excerpt: 'Today I will be having an interview with Roy Osherove about his upcoming book "The Art of Unit Testing".  The book concentrates on unit testing for the .Net developer. And it covers these subjects (as taken from the manning website).'
url: /index.php/architect/designingsoftware/an-interview-with-roy-osherove-autor-of/
views:
  - 5552
categories:
  - Designing Software
  - Enterprise Architecture
  - Introduction to Architecture and Design
tags:
  - interview
  - roy osherove
  - unit testing

---
Today I will be having an interview with [Roy Osherove][1] about his upcoming book &#8220;[The Art of Unit Testing][2]&#8220;. The book concentrates on unit testing for the .Net developer. And it covers these subjects (as taken from the manning website).

* Introduction to unit testing and the basics of writing real-world unit tests with NUnit
      
* Best practices for writing maintainable, trustworthy, readable tests
      
* Mock Objects and integration testing in-depth
      
* Reference chapters for NUnit, Rhino.Mocks, and TypeMock.NET
      
* Database unit testing
      
* Testing legacy code
      
* Designing for testability

You can wait for the [hard copy to come out in the beginning of 2009 or buy the Early access edition now][3].

**1. Do you think unit-testing is for the beginner level user or for the advanced level user? And is your book geared toward the beginner level unit-tester, the more advanced level unit-tester or the what-is-unit-testing user?**

Unit Testing is the basic activity that any developer should be involved with, from entry level to the most advanced.
  
It should be as natural to do as debugging is, and should be viewed as part of daily life for developers. My book is geared towards three crowds: Those who don&#8217;t even know what unit testing is, those who do but want to learn how to write good tests, and those who have experience and would like to advance it deepen it.
  
The book strictly deals with Unit testing, not test driven development.

**
  
2. Do you think schools should teach more and better unit-testing skills or is that something a developer should learn on the job? And how long does it take to become a good unit-tester?**

I think unit testing should be a skill that is taught in schools. Absolutely. I think that is far from happening though&#8230;
  
Learning the basics of unit testing (the API) takes an hour, not more. Learning the basic three pillars &#8211; maintainable, readable and trustworthy takes longer, mostly on the job. but you&#8217;d need to have guidelines to follow.
  
it&#8217;s a bit like asking how long it takes to learn good coding conventions. It&#8217;s on the job training but you need to have guidance.
  
That said, I think that learning how to use a mocking framework, the idea of mock and stub, dependency injection and such can take a while, several weeks of getting used to things. without guidance &#8211; much longer. I&#8217;m hoping that with frameworks like Typemock Isolator this barrier will grow smaller and smaller.

**3. What do you think will be the future of unit-testing? Will it be TDD or more BDD? Will it be wizard driven? For example, you write a small block of code, click generate and the system writes your tests? Will MS-Test become the standard?**

I think we will see more BDD-like syntax trying to emerge (right now it is very chaotic and confusing to me). I think We will see more tooling around unit testing such as Pex, MbUnitGallioTypemock Isolator etc.. and less focus on basic APIs. Model based testing and DSLs for testing will try to take a bigger role.
  
MS test will become more and more prevalent and we will see more standardization in unit testing and mocking frameworks just like we start to see in the IoC space (IServicceLocator).
  
wizard based test generators will be more prevalent, I think.

**4. How important is the role of Microsoft in all this? Will more emphasis from them help the community do more unit-testing? Or is the RAD/Wizard Driven development community to strong?**

Microsoft having unit testing in the box is a big help towards making this ubiquitous. I don&#8217;t think that the RADWizard driven community is at odds with having unit tests. Instead, we should be making unit testing easier to accomplish. right now the barrier is too high for most people. That does not mean they are bad programmers because they don&#8217;t have time or face a lot of organizational difficulties introducing unit testing.
  
Whenever something is easier to do than not doing it, people will do it. When it becomes easier to just write unit tests for code instead of debugging for 3 days straight to find a small bug, we&#8217;ll see the chasm crossed. 

**5. The .Net world and the unit-testing world seem to be in a constant state of flux. It seems like there is something new to learn every week.
  
Frameworks change, frameworks are added. How do you handle that change?**

I read a lot of blogs, and download lots of zip files.
  
but I usually don&#8217;t stick with something before it becomes a little more mainstream. I might give Moq a try, I might check out Pex, but it will always be a red flag to me until I can make sure it has enough of a following to justify a real usage in a real project (hope grown projects don&#8217;t count &#8211; they are my playground &#8211; and where I learn lots of things)

**6. What other resources do you recommend apart from your book to start learning about unit-testing. Can you name a few?**

That&#8217;s the thing &#8211; there aren&#8217;t a lot of books that deal with this that don&#8217;t also deal with Test Driven Development. I started with reading &#8220;Test Driven Development with Microsoft.NET by Jim Newkirk, http://www.testdriven.com is nice. Frankly, I don&#8217;t know a good resource on \*just\* unit testing that does not throw in TDD into the mix. I think that&#8217;s a sign that we are coupling the two things too much. Getting started with TDD is much harder than getting started with unit testing.
  
I am starting a section on &#8220;how to get started with Unit Testing&#8221; on my book wiki and I&#8217;m encouraging people to help out filling information in it.

**7. Good unit-testing is about getting maintainable, readable and trustworthy tests. This seems the same for good code. So having good unit-tests means you have good code? Or is it a little more subtle then that?**

Unit Tests should be considered part of the production code. they should be refactored and they should have the same qualities, with a little extra on top: because a unit test represents a \*usage case\* of a piece of code, it should have a more &#8220;declarative&#8221; quality that can be easily read and reviews. it should show intent. 

trust worthy, among other things, means you are sure that you are indeed testing the right thing. that takes care of making sure your code in production actually working, so, yes. in essence, if your tests provide all those qualities, depending in part on the amount of coverage you have, you can be reasonable sure you have good code.
  
Sadly, all three pillars are all but forgotten in most literature that deals with unit tests

**8. What new features do you want in future versions of the .Net framework and your favorite unit testing frameworks?**

I don&#8217;t have anything specific I can think of right now. I&#8217;m looking forward to IronRuby.
  
In unit test frameworks I think we are pretty much &#8220;stuck&#8221; where we are and its time to see a new breed of next generation test frameworks that take us a step further. from model based testing to StoryTeller (Fit) and acceptance testing, i hope the next 3 years will be just as exciting as the past 3 were.

**9. What did you learn from writing this book? Does writing books come easy to you? Are you planning more books?**

I learned that writing a book is pretty hard work, mainly. I&#8217;m not planning another book any time soon.
  
I plan to add any accumulated knowledge in the book&#8217;s [wiki site][4] (chapters that should have been written&#8230;etc)

**10. What does the future look like for you? Any conferences, seminars or trade shows perhaps?**

In the near future I&#8217;ll be speaking TechEd Europe in Barcelona this November. During December and throughout next year I&#8217;ll be holding Unit Testing at Test Driven Development Courses throughout Norway. Other than that I&#8217;ll continue to work on the next generation of unit testing tools at Typemock. 

Thank you Roy for this interview. If people want to learn more about unit testing I invite them to read [Roy&#8217;s book][2]. And his [blog][1].

 [1]: http://weblogs.asp.net/rosherove/default.aspx
 [2]: http://artofunittesting.com/
 [3]: http://www.manning.com/affiliate/idevaffiliate.php?id=462_91
 [4]: http://artofunittesting.com