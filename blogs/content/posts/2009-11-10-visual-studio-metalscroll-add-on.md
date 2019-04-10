---
title: Visual Studio – MetalScroll Add-On
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2009-11-10T09:04:49+00:00
ID: 617
url: /index.php/desktopdev/mstech/visual-studio-metalscroll-add-on/
views:
  - 7702
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET

---
While I may use PHP here at the LessThanDot site, I've been using Visual Studio and .Net since the first .Net release (and bribery in the form of scotch may convince me to share some of my pre-.Net experiences as well). While I have tried a few .Net plugins over time, my standard Visual Studio layout is actually only a very lightly customized version of the standard out-of-the-box settings. Perhaps I change the settings for recent projects to display a longer list, add some additional User Task types, and tweak other small settings, but overall my experience is pretty vanilla.

About a week ago I decided to try a small plugin and, based on my fairly vanilla .Net existence, expected it to be an interesting week that would end in me uninstalling the little guy. The plugin purported to make the scrollbar more useful by using it to “provide a graphical representation of the source code”. I have to admit that I was more intrigued about someone customizing the scrollbar to be useful than I was in visualizing my sourcecode (after all, I stare at this stuff all day long as it is).

## About MetalScroll

The name of this plugin is MetalScroll and it's hosted as an Open Source project at Google Code ([here][1]). The plugin turns your document scrollbar into a graphical representation of the source code for the current document by using a number of colors to map the presence of characters and other elements in the document area into pixels in the wider-than-average scroll area. A shaded area of the bar indicates your current screen position in the document and color-coding is provided for saved and unsaved code, breakpoints, comments, and active textual searches. The new scrollbar allows you to scroll as you normally would or hold down the middle-scroll button of your mouse for a quick highlight of an area of code.

![][2]

## My Thoughts

Overall this plugin only took a couple days to get used to. The ability to see a birds-eye view of my code has proven useful, allowing me to very quickly find individual items like break points or search results (hold Alt and doubleclick a word to highlight it throughout the current page). It also helps me orient myself faster when I land in a long chunk of code from a 'Search' or 'Go to Definition' command, as I only have to glance to the right and I have a 'you-are-here' perspective of the document I have ended up in, useful in projects with 5k+ line code files.

The only major downside I ran into was the lack of comment highlighting for VB.Net users (or perhaps a lack of understanding on my part to make it work). The company I am currently working with uses VB.Net as their standard in-house language and unfortunately this means that I don't get my comments in pretty green blobs in the scrollbar. This should by no means dissuade VB.Net users, however, as I did not actually know I was missing this functionality until a few minutes ago when I went to review the features on the website. 

While this add-on will not be to everyone's taste, I urge you to give it a try. After only a week of usage I have grown accustomed to having the bar beside my code and I have already reached the point where I start looking for it when I visit other peoples desktops. I would also urge you to let the author know you appreciate his work if you do begin using it regularly, as he is likely working on this in his spare time and it is always nice to know that other developers appreciate your hard work.

### Download Information

You can download this plugin at the following site: <http://code.google.com/p/metalscroll/>

 [1]: http://code.google.com/p/metalscroll/
 [2]: http://www.tiernok.com/downloads/MetalScrollSample.png