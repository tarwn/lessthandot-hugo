---
title: 'Friday SQL Nugget #1 post'
author: SQLDenis
type: post
date: 2012-01-13T17:03:00+00:00
ID: 1489
excerpt: |
  I was tagged by Ted Krueger in his Friday SQL Nugget #1 post. This Friday's nugget is: Deciding I need to delete it all and start over again.
  
  
  Since programmers are lazy, they usually prefer to make changes to existing tables/packages/stored procedu&hellip;
url: /index.php/datamgmt/datadesign/friday-sql-nugget-1-post/
views:
  - 2573
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - agile
  - starting over

---
I was tagged by Ted Krueger in his [Friday SQL Nugget #1][1] post. This Friday's nugget is: Deciding I need to delete it all and start over again.

Since programmers are lazy, they usually prefer to make changes to existing tables/packages/stored procedures to make something fit in instead of starting from scratch. This makes sense usually because the change is small and you don't have to test that much. But then another changes comes along...and another...and another...and before you know it, you have created a patched up solution with so many band-aids that you better be standing close by with that mop to clean up all the leaks

Another reason that this happen is that you inherit someone else's code and you are not quite sure how it all works, you add something to it, you didn't break functionality and you are happy. There is no ROI on reinventing the wheel and the business people will ask you why you are doing just that when all they want is a small change, these people don't understand what is involved with programming, not everything is a silo

I am guilty of patching things as well, this is usually done because of time constraints. When you redesign something, you start with a clean slate, there is nothing that holds you back because you have something that needs to be in a certain table or format...this is what views are for 🙂

When I redesigned our current web reporting functionality, it was about 1000% faster, no code changes were needed from the applications because I created views that had the same name as the old tables.

I also redesigned another system because it was a maintenance nightmare, whenever you wanted to add a new product, you would have to rebuild 5 tables in the correct order and with correct sorting. This took about 30 minutes each time. I was quickly fed up with that, I recreated this from scratch and now it takes one minute to do the same thing.

If you get a new person to redesign an old application, do **NOT** show this person the old application. Let this person gather requirements and built the application from scratch if you don't do this what you will get is the same crappy application you had before and the users won't be happy. Remember, users don't know what they want, they only know what they don't want

Doing agile development where you deliver something every 2 weeks is a great way to get feedback often, you don't want to hear after you took 9 months to build the application that this is not what they really wanted. Plan you sprints accordingly, get a business stakeholder on board to help you out with the sprint planning process.

I will leave you with an excerpt from someone's post, I think it is fitting

> Tonight I was hungry. I wasn't at home. I couldn't go to the kitchen for a snack. I looked around and saw only McDonald's across the street.
> 
> Then I was struck with the same dilemma that I face whenever I leave the comfort of my own home for any decent spread of time: Do I eat crappy food now and satisfy that hunger? Or do I stay hungry for a little longer and eat a healthy meal back at home?
> 
> As I pondered this dilemma I couldn't help but notice how much it relates to code quality. 

You can read the complete post here: http://jstorimer.com/2012/01/09/the-hungry-programmer.html

 [1]: /index.php/ITProfessionals/ProfessionalDevelopment/friday-sql-nugget-1