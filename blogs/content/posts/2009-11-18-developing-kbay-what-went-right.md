---
title: "Developing KBay: It's All About the Team"
author: ThatRickGuy
type: post
date: 2009-11-18T13:14:59+00:00
ID: 632
url: /index.php/webdev/webdesigngraphicsstyling/developing-kbay-what-went-right/
views:
  - 3788
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - development
  - silverlight
  - team

---
Hello Everyone,

<p style="text-indent: 30pt;">
  Rick here again with another little blurb on my most recent Silverlight project, KBay. Today, I'd like to write a little bit about what went right.
</p>

<img src="http://ringdev.com/code/KBayHome.PNG" alt="KBay Home Page" title="KBay Home Page " style="margin-left:50px;" />

### Separation of Power

<p style="text-indent: 30pt;">
  One of the biggest attributes of KBay's success, IMO, was the segregation of development. My coworker Adam (Hi Adam!) and I handled the majority of the work for this project. The initial goal was to try to involve everyone on the App Dev team, to give them more exposure to Silverlight. But with the tight time-line and other priorities, we couldn't keep the whole team on page. So after some initial design meetings and DB layout, it largely fell to Adam and me to get it to work.
</p>

### Balance Your Strengths

<p style="text-indent: 30pt;">
  Adam and I approached the problem from our own strengths. Both of us are more than capable of writing the entire solution, but we both have our strengths and weaknesses. In my case, my experience with Blend and XAML, along with some rudimentary graphics arts knowledge, really allowed me to speed through the front end, while Adam's envelope pushing experience with LINQ and Generics allowed him to come up with some really awesome solutions to communication and technical issues.
</p>

### Two Heads are Better Than One

<p style="text-indent: 30pt;">
  This separation really allowed us to greatly improve not just our development speed, but it also worked as a sort of code review, where we were both interacting with each other's interfaces. Since we had virtually no requirements for the project, it allowed us to brainstorm freely and to consider many options that neither of us would have thought of on our own.
</p>

### Lather, Rinse, Repeat

<p style="text-indent: 30pt;">
  Both of us were so struck by how excellently this approach worked in our environment, that we have recommended to our management that we reuse it in more projects . And so far, it has continued to work exceptionally well.
</p>

### One of These Things is Not Like the Others

<p style="text-indent: 30pt;">
  Working in Silverlight, unlike any other mainstream MS platform, means that your only option for interacting with the server is via Webservice or WCF calls. Not only are these the only options (aside from some socket options) but they are also entirely Async in their behavior.
</p>

### Limits Are Good?

<p style="text-indent: 30pt;">
  This limitation opens up a number of options and challenges. First, there is a crystal clear separation between the presentation layer and the business/data layer. It allows for developers to be very pure on either side. A web service does nothing for layout and the UI does nothing for data manipulation (save for maybe some caching).
</p>

### Green Light Go! Red Light Stop!

<p style="text-indent: 30pt;">
  Second, the UI developer can track explicitly when any IO begins and ends, and with a bit of wrapping, control the UI accordingly.
</p>

### Unit Testing Made Easy!

<p style="text-indent: 30pt;">
  Third, and probably best of all, since almost all of the functionality is being controlled by web services, it is really easy to isolate functionality for integrated unit testing. This has been a huge boon for us in terms of stability of deployments, even in the dev environment with multiple developers. It takes 20 seconds to run every single method through all of our known use cases, and those unit tests are run before every deployment. These unit tests are a critical to reducing risks for off-schedule deployments.
</p>

### Are Limits Bad?

<p style="text-indent: 30pt;">
  Maybe 'limits' isn't the right word for this. You can still do almost everything you can do through other platforms, you just have to go about it through entirely different means. The limits that exist force developers to go through the new means, using async communication and webservices/WCF instead of continuing to depend on integrated IO and synchronous processing. So while there is a learning curve in changing the way we approach problems in Silverlight, I think that overcoming that curve will enable more developers to think in a parallel style. Removing the interface from the process, and making the processes async forces developers to partake in good development practices. Because every time I see a Windows app go to a white screen because some developer tried running I/O operations on the GUI thread, I cry a little on the inside.
</p>

### I Love Log

<p style="text-indent: 30pt;">
  That's it for the more abstract summary of the KBay project. I promise my next segment will deal with more of the technical nuts and bolts of the project. Including the communication layer and my personal favorite, LOGGING!!!
</p>

-Rick