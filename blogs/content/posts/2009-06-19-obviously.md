---
title: Obviously!
author: chopstik
type: post
date: 2009-06-19T16:05:14+00:00
url: /index.php/itprofessionals/ethicsit/obviously/
views:
  - 5506
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT

---
Recently, I was having to test an application to which I had made some modifications to fix an error that had been discovered. In the course of testing the changes I had done, I found myself with an error that was disturbingly familiar. The application was an ASP app and the error was a server.createobject error indicating that the object could not be created. I have come across this error at least a few times previously but it had been a while. As I normally do when I find myself in a situation that needs a resolution, I went to Google (though I will have to start using Bing to see how well it performs &#8211; much as SQLDenis has noted in one of his recent blog entries). My search results came back rather unsatisfyingly with answers that I had already looked at and confirmed were not really issues. I checked again by running through the tests and it was still failing at the point of creating the object.

Somewhat exasperated and not a little sheepishly, I found a co-worker and walked through it on their machine &#8211; it worked as expected. So we came back to my desk and, after going through several steps to see if we could pinpoint the error, my co-worker noticed that it looked like I had the .dll file for the object but I did not actually have the application installed on my machine (in this case, it was the PolarZip application). Sure enough, I did not have it loaded. Five minutes later, the PolarZip application was installed on my machine and the ASP app was functioning as it should. Fortunately, my co-worker did not spend the rest of the day glorying in my looking like a complete idiot but it was a lesson well learned in my case.

The point of this story, though, is that sometimes it is the most obvious things that can trip us up. Normally, when I do my development, I tend to look at the big picture to see what is happening and what will be the best solution. If I encounter an error, I try to trace back to the source so that I can determine what is causing the issue and then make my determination as to the best solution. In this case, the error was plainly obvious and one that I had encountered before &#8211; but not due to the same cause as this time (normally it was because the required .dll files were not properly registered or had become corrupted and therefore needed to be replaced). However, in this instance, I was so caught up in my earlier recollections I overlooked one of the most obvious potential causes &#8211; one that would normally not even be considered because \*of course\* the application is loaded on the machine!

Nonetheless, the lesson was learned and I will have to remember one of Spock&#8217;s (Star Trek, not Doctor) answers when facing a difficult problem: disregard the impossible and then, whatever is left, no matter how improbable or unlikely, is the answer.