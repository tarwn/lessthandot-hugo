---
title: Unit tests are not enough for validation of your method in scientific applications
author: Christiaan Baes (chrissie1)
type: post
date: 2011-04-21T09:11:00+00:00
ID: 1122
excerpt: |
  A while ago I wrote a little application that looks for a fiber in an image and then calculates the average color and shows the user the hue. 
  
  
    Aforge.Net and deciding the average color of a fiber
    Determining the average color of a fiber
  
  
  I&hellip;
url: /index.php/desktopdev/mstech/unittests-are-not-enough-for/
views:
  - 1534
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET

---
## Introduction

A while ago I wrote a little application that could be useful in our research (forensic fiber examination).

I have a bunch of unit tests and integration tests that prove that what I do works on a programmatic scale. This was the easy part. If you print the whole application I guess it would all fit on 10 pages (haven&#8217;t tried, so pure guess work). 

And now comes the hard part. I have to validate my method in such a way that it becomes useful for our lab to use in a routine analysis.

For this I have to prove that the method will give the same result when used by different operators. I have to prove that the results are the same over time. And if the results are not the same then what is the margin of error we can expect. 

## Intra variability.

First I want to prove that this method will give the same result when used on the same object. This is easy. Just do 5 measurements and see if the standard deviation is reasonable. We can of course do a statistical analysis on this to prove that the results are acceptable. And we will.

## Inter variability

This is where we measure similar objects at the same time to see if the results are similar too. 

## Time related variability

This is where I do the same 5 measurements on the same object spread over time. For example: after an hour, after 6 hours, after a day, a week and a month. This way I&#8217;m sure the results don&#8217;t change to much over time or how much they change over time.

## Reproducibility

To check the reproducibility of my results I need to get some other people involved to see if they get the same results when measuring the same objects.

## Equipment

The equipment you use to take the measurements can have parameters you need to set before doing the measurement, do they have an effect on your measurement and consequent result. 

## The result

After you have all this on paper you can then go out and make a procedure on how the users should go about using your application to get the best results. Show them what not to do and what to do. Tell them what are and what are not acceptable results.

## Conclusion

Although the application gives the result I think it should, that does not mean I have to take them for granted. A lot of testing will be needed to determine it&#8217;s boundaries and usability. This will take a lot more time and effort then writing the application. But it is fun none the less. Sorry, no pictures, no code.