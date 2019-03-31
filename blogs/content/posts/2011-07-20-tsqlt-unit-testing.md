---
title: tSQLt Unit Testing
author: George Mastros (gmmastros)
type: post
date: 2011-07-20T15:25:00+00:00
excerpt: |
  Yesterday, I attended a training class at Microsoft’s facility in Malvern, Pennsylvania.  This training class was led by Sebastian Meine (sqlity.net) and Dennis Lloyd (curiouslycorrect.com). 
  
  The class was from 9:00 am to 5:00 pm with a short break f&hellip;
url: /index.php/datamgmt/datadesign/tsqlt-unit-testing/
views:
  - 12771
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server

---
Yesterday, I attended a training class at Microsoft’s facility in Malvern, Pennsylvania. This training class was led by Sebastian Meine ([sqlity.net][1]) and Dennis Lloyd ([curiouslycorrect.com][2]). 

The class was from 9:00 am to 5:00 pm with a short break for lunch. During the class, Dennis and Sebastian explained how to use tSQLt (<http://tSQLt.org>) to write unit tests for your database code. tSQLt is a frame work that can be freely downloaded and applied to your database, allowing you to quickly and easily write unit tests. After learning about the framework and working through the exercises during the class, it is immediately obvious to me how this framework and the techniques explained during the class will benefit my organization.

Specifically, writing unit tests for the database will allow me to re-factor the code in a safe way, making sure that the code doesn’t break because I can easily run all of the unit tests for the database, or just the unit tests associated with the code I am in the process of changing.

Since I already have dozens of views, hundreds of functions and thousands of stored procedures, I cannot take the time to write all the unit tests required for the existing stuff, but I will create unit tests for the new code I write and also unit tests for any bug fixes with the existing code. Over time I will have a set of unit tests for my database code that will undoubtedly allow me to spend less time fixing defects and more time writing new functionality.
  
With my application, most of the bugs discovered by the end user are data related. The tSQLt unit testing framework will allow me to write tests for those bugs and then have confidence that the bug will not return (in the released version of the software).

Thank you Dennis and Sebastian for teaching this class and showing me this framework. I certainly appreciate it and will be sure to start using it.

 [1]: http://sqlity.net
 [2]: http://curiouslycorrect.com