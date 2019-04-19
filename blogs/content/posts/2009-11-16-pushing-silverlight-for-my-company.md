---
title: Pushing Silverlight for my Company
author: ThatRickGuy
type: post
date: 2009-11-16T20:09:58+00:00
ID: 631
excerpt: "It's me again, Rick the Silverlight nut. I've been buried under a pile of work this last month and haven't had a chance to post much of the fun stuff I've been working on. But I've got a few minutes to write about my last big project. So I give you, the story of KBay"
url: /index.php/webdev/webdesigngraphicsstyling/pushing-silverlight-for-my-company/
views:
  - 4952
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - silverlight

---
Hey everyone,

<p style="text-indent: 30pt;">
  It's me again, Rick the Silverlight nut. I've been buried under a pile of work this last month and haven't had a chance to post much of the fun stuff I've been working on. But I've got a few minutes to write about my last big project. So I give you, the story of KBay:
</p>

<img src="http://ringdev.com/code/KBay.PNG" alt="KBay sample screen" title="KBay Sample Screen" style="margin-left:50px;" />

### The Story So Far...

<p style="text-indent: 30pt;">
  I work for Kerry Americas, a food product company with a corporate office in Beloit, Wisconsin. Over the last year we have relocated much of our sales, service, R and D, and other folks to a brand new, state of the art office building and production facility. This place rocks. This did however, leave us with a whole lot of cruddy old furniture, that had been well-used in many of our older office spaces. So the building managers, remembering that almost a decade ago the IT shop had given them an EBay-like app used to auction off goods for charity, gave us a call. They wanted us to fire up this archaic app so they could try to move the furniture to employees for charity. My boss, seeing an opportunity for something better, got us 2 weeks to get something running. We could either get the classic site up and running again, or make one from scratch in Silverlight, but it had to be entirely off the books.
</p>

### Racing the Clock...

<p style="text-indent: 30pt;">
  So we worked our fingers to the bone pulling it off after hours, over lunch, etc, but after 9 days we had a working prototype. On day 10 we demo'd it to the users and they were ecstatic. We had 3 weeks to the auction and we used some of that time to refine and improve the system (I hope to be posting about our communication frame work shortly!)
</p>

### Going Live...

<p style="text-indent: 30pt;">
  Last week, Monday morning we went live with over 200 auctions and almost 600 available users. We had a huge load on the server that morning, but everything stayed up and stable. No crashes, no complaints. We had a few minor bugs, and since it was a non-critical app the boss-man gave us the go-ahead to push to production off-schedule.
</p>

### Down to the Wire...

<p style="text-indent: 30pt;">
  Friday evening was the big test though. All of the auctions ended at the exact same time. The managers were joking that they had never seen the parking lot so full on a Friday night. And the web server was showing some severe strain. With 300+ users sitting on their favored auction's page, hitting refresh over and over again, the server was having issues keeping up. But it never crashed, it just got slow to access the site.
</p>

### In the End...

<p style="text-indent: 30pt;">
  It was a huge success! The company off loaded almost all of the auctions. The employees got some great deals on some impressive furniture. A local charity food bank picked up a few thousand dollars. And we got Silverlight installed on almost every PC in the building AND got some major good-credit with the users.
</p>

### The Post Mortem...

<p style="text-indent: 30pt;">
  And post-hoc analysis is pointing to an obvious short coming in our interactions. Since we had developed the application so quickly, we had cut some corners. One of those corners was image caching. Each time users were browsing auctions they were downloading every thumbnail, and every time they looked at a single auction, they would download the full sized image. We had mentioned caching briefly during design, but with a 9 day window, we scrapped it and it was forgotten about while we polished. But looking back now, we could have just hosted the images via an ASP.Net page. Not only would that have solved the hosting issue, but it would have also allowed Silverlight to use the built in browser cache for all of the images. A full caching system without writing a single line of code. We're kicking ourselves now for not seeing it before, but we'll have it in for the next auction!
</p>

### More to Come...

<p style="text-indent: 30pt;">
  I hope to have time to post some more of what we learned on this project soon, but I've got another hot deadline approaching for a Silverlight application that mocks Share Point inside of Sales Force.
</p>

-Rick