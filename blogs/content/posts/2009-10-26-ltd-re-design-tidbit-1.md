---
title: 'LTD Re-Design Tidbit #1'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2009-10-26T21:54:16+00:00
url: /index.php/webdev/webdesigngraphicsstyling/ltd-re-design-tidbit-1/
views:
  - 6003
rp4wp_auto_linked:
  - 1
categories:
  - UI Development
  - Web Design, Graphics and Styling

---
While several of our members are studiously assisting us by using (and abusing) the beta version of the new layout, I have to admit that I am still hard at work. Well, actually, no, I&#8217;m sitting on the couch with the netbook and a beer trying to ensure the pillows don&#8217;t start floating away, but I am thinking hard.
  
Honest.

## I Like Our Layout

Don&#8217;t misunderstand, I do like our current design and I didn&#8217;t start this process because I was tired of the design. What was bothering me the most was seeing all of the good articles we were posting and all the statistics from people that could bear to read our blogs section and knowing that there probably just as many who weren&#8217;t bothering (like myself) simply because of the layout.
  
Well, and I really disliked how we implemented the layout for the blogs. But lets pretend it was just the higher, less selfish purpose that motivated me.

### Layout Changes for Readability

The first major change was to constrain the width of the blogs to allow a more comfortable reading experience. I, like many others, have gotten used to widescreen monitors and found reading unconstrained paragraphs on a 16:9 or 16:10 monitor to be tough. In keeping with the reported findings from other blogs and websites (such as [this article][1]), we settled on a more defined and consistently thinner area for the article text. We used a semi-fluid layout to provide a good reading experience to smaller resolutions (1024x&#8212;) but take advantage of a little additional space for larger resolutions (1280+x&#8212;). The reason we call this semi-fluid is that we only shrink and expand within the widths listed above, preventing the page from wrapping too much text or growing uncomfortably wide.

While the final measurements are being tested right now by our beta members, we feel the combination of constraints on the width and fluid layout should make for a cleaner and less annoying reading experience. 

<center>
  <img src="http://www.tiernok.com/downloads/LTD/Tidbit_7.png" alt="LTD Tidbit Image" />
</center>

In Technical-ese? We took advantage of the fact that most of you will finally be off of IE6 and that let us use some of the newer CSS attributes that Microsoft has finally started to implement, such as min-width and max-width. There may also be a little JQuery trickery involved by the time we launch to make the margins even more fluidy (keep your scrabble rules to yourself), but this will be dependent on beta feedback.

(Late Addition: There is indeed some jquery magic now for those of you using the [8 year old][2] browser. Feel free to buy me a beer to make up for that evening of my life&#8230;)

### Color and Font Changes

While black text on a light, slate-blue background is easy enough to read, it doesn&#8217;t provide a consistent experience when the various headers (H1, H2, titles, etc) do not follow a clear relationship in size, font, decoration or color. To provide a clear focal area, the greyish blue has been replaced with a white background. Elements of grey and a greyish green remain, but these are used as the background for secondary areas, such as the sidebar or page background.

<center>
  <img src="http://www.tiernok.com/downloads/LTD/Tidbit_8.png" alt="LTD Tidbit Image" />
</center>

The close-to-50 font family declarations have been replaced by approximately 2 and standard definitions for links, headers, and various other elements were created to replace sections that often had definitions composed of accretion from 3 or 4 areas of the site. Sub-sites now enhance the standards when necessary instead of redefining them.

In Beer-ese? We take big-big CSS files, whack them with a delete button, and make teeny new CSS files. All better. (Except for phpBB, there is no axe large enough)

## And more&#8230;

There are more changes still, the next tidbit entry will cover a little more about the changes we made to the major page elements (yawn) and social networking (less-yawn).

 [1]: http://www.smashingmagazine.com/2009/08/20/typographic-design-survey-best-practices-from-the-best-blogs/
 [2]: http://en.wikipedia.org/wiki/Internet_Explorer_6