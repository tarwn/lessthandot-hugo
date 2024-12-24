---
title: Running sandcastle project from teamcity
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-04T11:06:00+00:00
ID: 1289
excerpt: Now that you have all those nice XML comments on your methods and classes and everything else it is time to make them into something no one will ever read but you can use to impress the manager (especially useful in printed format). I will use sandcastle and teamcity for this.
url: /index.php/itprofessionals/softwareandconfigmgmt/running-sandcastle-project-from-teamcity/
views:
  - 12472
categories:
  - IT Processes
  - Software and Configuration Management
tags:
  - sandcastle
  - teamcity

---
## Introduction

Now that you have all those nice XML comments on your methods and classes and everything else it is time to make them into something no one will ever read but you can use to impress the manager (especially useful in printed format). I will use sandcastle for this. 

## Sandcastle

I just [installed sandcastle][1] as one would do. Then make your project using the GUI and save it somewhere that makes sense. 

So nothing like this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/sandcastle1.png?mtime=1312462687"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/sandcastle1.png?mtime=1312462687" width="1024" height="682" /></a>
</div>

## Teamcity

So now that you have your sandcastle project all configured and every bit of documentation squeezed out of your application, the more the better (don&#8217;t worry no one will ever read it anyway). And I might you have probably wasted hours just waiting for sandcastle to do it&#8217;s business. We can now move on to configuring teamcity.

This is the easy part. And here you have it.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/sandcastle2.png?mtime=1312462696"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/sandcastle2.png?mtime=1312462696" width="717" height="673" /></a>
</div>

Yes, your sandcastle project can be built with MSBuild and yes, you have to set the path to your project. And you don&#8217;t want to forget to set the correct .Net version.

After all this you can run it and go to sleep for a few hours while sandcastle does it&#8217;s thing. 

I make this a weekly build since I don&#8217;t think it is all that important.

## Conclusion

Once you know how it is pretty easy to set up and since you read this blogpost you will have it set up in a matter of days, since you will have to wait for sandcastle to finish.

 [1]: http://shfb.codeplex.com/