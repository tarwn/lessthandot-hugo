---
title: Behaviour-driven development and User stories
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-17T11:10:31+00:00
ID: 143
url: /index.php/architect/introductionarchitecturedesign/behaviour-driven-development-and-user-st/
views:
  - 4644
categories:
  - Introduction to Architecture and Design
tags:
  - bdd
  - use case
  - user stories

---
On [Dan North][1]&#8216;s blog you will find a nice writeup on this subject the post is called [What’s in a Story?][2] and explains it a bit better. 

Compare to use cases where you describe the interaction between the stakeholder and the other stakeholder (mainly the system) this describes the desired behaviour of the system.

This is an example of such a use case. Found [here.][3]

> Use Case Example
  
> Example for the Club Information System (CIS)
  
> Actor: A member of the public (MP)
  
> Use case: The MP is searching for club events on a particular date.
  
> Preconditions: The MP is at the CIS home page, but not logged in as a member.
  
> Scenario A:
  
> 1. MP selects “Search Events” on MP home page
  
> 2. System presents a page with choice of dates for the current month
  
> 3. MP selects a date from among the choices
  
> 4. System presents a page with events for that date, giving time and club name
  
> 5. MP selects an event
  
> 6. System presents a page with details of that event, including location, description and cost
  
> Exception:
  
> 4. If there are no events for the selected date, System presents a page saying that there are no
  
> events for the selected date
  
> Alternative Scenario A1:
  
> 3a. MP selects a different month
  
> 3b. System presents a page with choice of dates for the current month
  
> Actor: A club officer (CO)
  
> Use case: The CO is adding a new event for the club

This is an example of such a story copied from .

> Story: Account Holder withdraws cash
> 
> As an Account Holder
  
> I want to withdraw cash from an ATM
  
> So that I can get money when the bank is closed
> 
> Scenario 1: Account has sufficient funds
  
> Given the account balance is $100
   
> And the card is valid
   
> And the machine contains enough money
  
> When the Account Holder requests $20
  
> Then the ATM should dispense $20
   
> And the account balance should be $80
   
> And the card should be returned
> 
> Scenario 2: Account has insufficient funds
  
> Given the account balance is $10
   
> And the card is valid
   
> And the machine contains enough money
  
> When the Account Holder requests $20
  
> Then the ATM should not dispense any money
   
> And the ATM should say there are insufficient funds
   
> And the account balance should be $20
   
> And the card should be returned
> 
> Scenario 3: Card has been disabled
  
> Given the card is disabled
  
> When the Account Holder requests $20
  
> Then the ATM should retain the card
  
> And the ATM should say the card has been retained
> 
> Scenario 4: The ATM has insufficient funds
  
> &#8230;

I&#8217;m not sure what I like best. I think both serve the same purpose in the end and that is to make the system a good one and one that the user is willing to use. In the end the stakeholder doesn&#8217;t care how you got there but he cares on how it can be of use to him hence the name user. if the system is not useful then the user will go to another system. And that is the number one rule in software development. Else you are just wasting money and time.

 [1]: http://dannorth.net/
 [2]: http://dannorth.net/whats-in-a-story
 [3]: http://www.google.be/url?sa=t&source=web&ct=res&cd=3&url=http%3A%2F%2Fweb.cecs.pdx.edu%2F~maier%2F386%2Fusecase06.pdf&ei=2ALRSLe_G5zE1wb35risDQ&usg=AFQjCNFGdt4NkkFjVsGRn3mN4nMQmjpspQ&sig2=OXtn7NKdIHy04q2aZ1IBtg