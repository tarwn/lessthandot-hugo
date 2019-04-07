---
title: The History of HTML Table Layouts
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-08-02T10:01:00+00:00
ID: 1233
excerpt: "The argument over table vs non-table layouts has been going on for years. I remember seeing online conversations as far back as ten years ago on the topic, and given my notoriously bad memory that should be taken as a minimum. Along the way we have dealt with partial CSS implementations, inconsistent rendering behavior, slow implementation of standards, competing proprietary implementations...it's been a long road."
url: /index.php/webdev/uidevelopment/xhtmlcss/history-of-html-table-layouts/
views:
  - 8134
rp4wp_auto_linked:
  - 1
categories:
  - XHTML and CSS
tags:
  - bad code

---
<div style="float: left; margin: .5em 1em .5em .5em;">
  <img src="http://tiernok.com/LTDBlog/rant.png" alt="Ranting Guy" style="height: 75px; " />
</div>

The argument over table vs non-table layouts has been going on for years. I remember seeing online conversations as far back as ten years ago on the topic, and given my notoriously bad memory that should be taken as a minimum. Along the way we have dealt with partial CSS implementations, inconsistent rendering behavior, slow implementation of standards, competing proprietary implementations&#8230;it&#8217;s been a long road.

Table layouts are still in use and in some places they are still the default. They are easy to implement and the browser and standards wars of the late 90&#8217;s and early 00&#8217;s have left behind a lot of people who remember how impossible it sometimes felt to make a non-tabular layout. However, while there are still browser inconsistencies and delays in standards adoptions, sometimes I wonder if we have forgotten just how long it has been since we started down this non-tabular path.

## Table-free Layout is &#8220;New&#8221;

Tables have existed as part of web standards since the [HTML 3.2 standard][1] (January 1997). The standard referenced an earlier RFC and was intended to be compliant with the table tags Netscape had already added to their browser, but this was their official addition to the HTML standard.

The standard pointed out that tables could be used for tabular data or layout purposes, but cautioned that using them for layout would impact accessibility.

<div style="margin: 0px 2em; font-style: italic; background-color: #eeeeee; padding: 4px;">
  Browsers, 1996-1997: MS IE3/4, NCSA Mosaic 2.1/3, Netscape 4, Opera 2/3
</div>

In [HTML 4][2] (April 1998), this warning was strengthened to &#8220;Tables should not be used purely as a means to layout document content&#8221;, and we were pointed to the addition of CSS1 to help accommodate this. It was noted, however, that using deprecated features was expected to continue for a little while in order to support older browsers (browser listed above).

In [HTML 4.01][3] (Dec 1999), this warning was strengthened to the following directive: &#8220;Tables should not be used purely as a means to layout document content&#8221;.

<div style="margin: 0px 2em; font-style: italic; background-color: #eeeeee; padding: 4px;">
  Browsers, 1998-1999: MS IE5, Netscape 4.5/4.6, Opera 3.5/3.6
</div>

Tables have existed as part of the HTML standard since 1997, we&#8217;ve been warned not to use them for layout, and CSS1 was added to the standard in 1998 to provide layout flexibility without having to resort to tables. While implementation of CSS1 was slow, the inconsistencies we saw between IE4, IE5, and NS 5.4 are a long time back.

If you are interested in the CSS side of the history:

[CSS1 Recommendation][4]
:   was published December, 1996

[CSS2 Recommendation][5]
:   was published May, 1998

[CSS2.1 Recommendation][6]
:   began in 2002 and was published in 2011 

[CSS3 at Wikipedia][7]
:   began in 1999 and is a collection of modular standards being published independantly

_Out of curiosity I pulled up my personal website from 2002 and checked the source. It was cross-browser compatible and the layout was table-free._

<div style="margin: 0px 2em; font-style: italic; background-color: #eeeeee; padding: 4px;">
  Browsers, 2002: MS IE6, NS 7, Mozilla 1, Opera 5.1.4
</div>

Fast-forward:

<div style="margin: 0px 2em; font-style: italic; background-color: #eeeeee; padding: 4px;">
  Browsers, 2006: MS IE7, NS 8.1, Firefox 2, Safari 2
</div>

Fast-forward:

<div style="margin: 0px 2em; font-style: italic; background-color: #eeeeee; padding: 4px;">
  Browsers, 2011: MS IE9, Firefox 4/5, Chrome 9/10/11/12, Safari 5
</div>



## CSS Takes Longer

There was a point in time when you could spend days trying to get the screen to layout properly. Oddly enough, with the slower internet connections available at the time, it was probably worth it to remove all the extra markup table layouts tend to incorporate.

The amount of time it takes to produce a layout in CSS versus tables has gotten fairly close. With the consistency shown by today&#8217;s browsers, the major gap in time is generally learning how to do it. That&#8217;s not to say that all is perfect in the world of browserdom, but it&#8217;s rare (unknown?) to be forced to use the table option given the number of available examples, prebuilt frameworks, and tweaks available from javascript packages.

Building a house is hard. Can the house builder cut corners to get your house up faster? Sure. But at the end of the day, if the builder is professional they won&#8217;t cut corners because those cut corners reduce the value of the house, reduce the satisfaction of the buyer, and increase the ongoing maintenance costs.

## The Exceptions

There were exceptions and they got fewer every year. As IE 5.5 and NS 4.6 faded from view, this grew to very few exceptions. Add things like jQuery to the mix and I would be hard-pressed to find an example that required the negative baggage table-layout brings with it. Initially we created fixed width layouts, then along came fluid layouts, then elastic layouts. The latest entry in the game is [responsive layouts][8], where we not only change the overall size of the page to accommodate browser sizes, but also element sizes and flow.

Resorting to table layout immediately means you aren&#8217;t treating it as an exception. It&#8217;s more likely you need to sit down with your manager or business and help them understand the ongoing costs involved with table layouts, some of the benefits available with newer techniques (ie, 2000 forward), and why it&#8217;s important to start making the switch. Luckily there are [resources][9] out there to help.

## There&#8217;s &#8220;No Time&#8221;

I understand deadlines, the pressure from the business to deliver, and the fact that many companies simply don&#8217;t bother to grow their people. As we mentioned before, it can be difficult to convince our managers and business to understand why we need to go the, initially at least, slower route of non-tabular layout.

But you need to go get started. Yesterday.

If your company is hesitant to let you spend the time, point out the costs involved with supporting new browsers as they come out, the advantages of good searchability, the wave of mobile devices that are flooding the market&#8230;clean markup can help all of these and there&#8217;s any number of sites out there that will provide longer, better articulated lists of costs and benefits (we&#8217;ve had a lot of time to think about it). 

And don&#8217;t forget to point out that since table layouts are contrary to the standard, what you are actually providing to your customer is a defective product.

## Moving Forward

When we follow standards, the browser developers can spend more time on new and interesting advancements. When we violate standards, we force them to spend time making sense out of the random, invalid junk we&#8217;ve sent to the browser.

Poorly structured, non-standard HTML (like table layouts) takes more bandwidth, takes longer for browsers to render, is more fragile, is harder for experienced web developers to work with, basically forces a rewrite for mobile devices, wastes the time and efforts of browser developers &#8230; it&#8217;s bad code. 

It might be acceptable for a beginner to use tables for layout, by definition a beginner is just learning the ropes. But part of being a beginner is learning and growing past that stage. With non-tabular layouts being around for 13 years, it&#8217;s long past the time we, as professionals, start requiring ourselves to know how to use them.

 [1]: http://www.w3.org/TR/REC-html32 "HTML 3.2 specification at W3C"
 [2]: http://www.w3.org/TR/1998/REC-html40-19980424/cover.html "HTML 4 specification at W3C"
 [3]: http://www.w3.org/TR/WD-html40/ "HTML 4.01 specification at W3C"
 [4]: http://www.w3.org/TR/CSS1/ "CSS1 Recommendation"
 [5]: http://www.w3.org/TR/2008/REC-CSS2-20080411/ "CSS2 Recommendation"
 [6]: http://www.w3.org/Style/CSS/specs#css21 "CSS2.1 Recommendation"
 [7]: http://en.wikipedia.org/wiki/Cascading_Style_Sheets#CSS_3 "CSS3 at Wikipedia"
 [8]: http://www.alistapart.com/articles/responsive-web-design/
 [9]: http://www.hotdesign.com/seybold/everything.html "Why tables for layout is stupid: problems defined, solutions offered"