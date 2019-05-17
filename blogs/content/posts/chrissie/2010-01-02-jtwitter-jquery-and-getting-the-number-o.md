---
title: jTwitter, jQuery and getting the number of followers
author: Christiaan Baes (chrissie1)
type: post
date: 2010-01-02T13:39:19+00:00
ID: 660
url: /index.php/webdev/webdesigngraphicsstyling/jtwitter-jquery-and-getting-the-number-o/
views:
  - 5970
categories:
  - Web Design, Graphics and Styling
tags:
  - jquery
  - jtwitter
  - twitter

---
Today I was having a bit of fun. And I wanted to add the number of followers we have for our LessThanDot Twitter account. 

I could have used the numerous services that give this feature already like [twittercounter][1]. Which I did on the [statistics][2] page. 

But those things are just copy paste and I wanted to show the number of followers in the title/tooltip for the twitter button in the social sitings sidebar section (Yes, Eli it now has a name). 

After some googling I found [jTwitter][3]. And I downloaded [version 1.1.0][4]. 

It just is a very small wrapper around the Twitter API and it returns the json document that the api uses. It is very easy to use, once you know how ;-).

After adding the jtwitter file as a dependency, like so:

```xml
&lt;script type="text/javascript" src="jquery.jtwitter.js"&gt;&lt;/script&gt;```
We can add this piece of script somewhere and see the magic happen:

```javascript
$(document).ready(function(){$.jTwitter('LessThanDot',1, function(userdata){
  			$('#social_twitter').attr({title: "Follow us on Twitter. Like " + userdata[0].user.followers_count + " others."});});});```
But you can do so much more with this.

Just look at what a twitter json object gives you:

> /*
      
> Example of returned object
  
> */
> 
> [
    
> {
      
> &#8216;in\_reply\_to\_user\_id&#8217;:null,
      
> &#8216;in\_reply\_to\_screen\_name&#8217;:null,
      
> &#8216;source&#8217;:&#8217;web&#8217;,
      
> &#8216;created_at&#8217;:&#8217;Fri Sep 18 06:11:15 +0000 2009&#8242;,
      
> &#8216;truncated&#8217;:false,
      
> &#8216;user&#8217;:{
        
> &#8216;profile\_background\_color&#8217;:&#8217;9ae4e8&#8242;,
        
> &#8216;description&#8217;:&#8217;jQuery and JavaScript howtos, tutorials, hacks, tips and performanace tests. Ask your jQuery questions here&#8230;&#8217;,
        
> &#8216;notifications&#8217;:false,
        
> &#8216;time_zone&#8217;:&#8217;Central Time (US & Canada)&#8217;,
        
> &#8216;url&#8217;:&#8217;http://jquery-howto.blogspot.com&#8217;,
        
> &#8216;screen_name&#8217;:&#8217;jQueryHowto&#8217;,
        
> &#8216;following&#8217;:true,
        
> &#8216;profile\_sidebar\_fill_color&#8217;:&#8217;e0ff92&#8242;,
        
> &#8216;created_at&#8217;:&#8217;Thu Mar 26 14:58:19 +0000 2009&#8242;,
        
> &#8216;profile\_sidebar\_border_color&#8217;:&#8217;87bc44&#8242;,
        
> &#8216;followers_count&#8217;:2042,
        
> &#8216;statuses_count&#8217;:505,
        
> &#8216;friends_count&#8217;:1015,
        
> &#8216;profile\_text\_color&#8217;:&#8217;000000&#8242;,
        
> &#8216;protected&#8217;:false,
        
> &#8216;profile\_background\_image_url&#8217;:&#8217;http://s.twimg.com/a/1253209888/images/themes/theme1/bg.png&#8217;,
        
> &#8216;location&#8217;:&#8221;,
        
> &#8216;name&#8217;:&#8217;jQuery HowTo&#8217;,
        
> &#8216;favourites_count&#8217;:15,
        
> &#8216;profile\_link\_color&#8217;:&#8217;0000ff&#8217;,
        
> &#8216;id&#8217;:26767000,
        
> &#8216;verified&#8217;:false,
        
> &#8216;profile\_background\_tile&#8217;:false,
        
> &#8216;utc_offset&#8217;:-21600,
        
> &#8216;profile\_image\_url&#8217;:&#8217;http://a3.twimg.com/profile\_images/110763033/jquery\_normal.gif&#8217;
        
> },
      
> &#8216;in\_reply\_to\_status\_id&#8217;:null,
      
> &#8216;id&#8217;:4073301536,
      
> &#8216;favorited&#8217;:false,
      
> &#8216;text&#8217;:&#8217;Host jQuery on Microsoft CDN servers http://retwt.me/2N3P&#8217;
    
> },
    
> {
      
> &#8216;in\_reply\_to\_user\_id&#8217;:null,
      
> &#8216;in\_reply\_to\_screen\_name&#8217;:null,
      
> &#8216;source&#8217;:&#8217;<a href="http://www.hootsuite.com" rel="nofollow">HootSuite</a>&#8216;,
      
> &#8216;created_at&#8217;:&#8217;Thu Sep 17 17:20:21 +0000 2009&#8242;,
      
> &#8216;truncated&#8217;:false,
      
> &#8216;user&#8217;:{
        
> &#8216;profile\_sidebar\_fill_color&#8217;:&#8217;e0ff92&#8242;,
        
> &#8216;description&#8217;:&#8217;jQuery and JavaScript howtos, tutorials, hacks, tips and performanace tests. Ask your jQuery questions here&#8230;&#8217;,
        
> &#8216;friends_count&#8217;:1015,
        
> &#8216;url&#8217;:&#8217;http://jquery-howto.blogspot.com&#8217;,
        
> &#8216;screen_name&#8217;:&#8217;jQueryHowto&#8217;,
        
> &#8216;following&#8217;:false,
        
> &#8216;profile\_sidebar\_border_color&#8217;:&#8217;87bc44&#8242;,
        
> &#8216;favourites_count&#8217;:15,
        
> &#8216;created_at&#8217;:&#8217;Thu Mar 26 14:58:19 +0000 2009&#8242;,
        
> &#8216;profile\_text\_color&#8217;:&#8217;000000&#8242;,
        
> &#8216;profile\_background\_image_url&#8217;:&#8217;http://s.twimg.com/a/1253141863/images/themes/theme1/bg.png&#8217;,
        
> &#8216;profile\_link\_color&#8217;:&#8217;0000ff&#8217;,
        
> &#8216;protected&#8217;:false,
        
> &#8216;verified&#8217;:false,
        
> &#8216;statuses_count&#8217;:504,
        
> &#8216;profile\_background\_tile&#8217;:false,
        
> &#8216;location&#8217;:&#8221;,
        
> &#8216;name&#8217;:&#8217;jQuery HowTo&#8217;,
        
> &#8216;profile\_background\_color&#8217;:&#8217;9ae4e8&#8242;,
        
> &#8216;id&#8217;:26767000,
        
> &#8216;notifications&#8217;:false,
        
> &#8216;time_zone&#8217;:&#8217;Central Time (US & Canada)&#8217;,
        
> &#8216;utc_offset&#8217;:-21600,
        
> &#8216;followers_count&#8217;:2038,
        
> &#8216;profile\_image\_url&#8217;:&#8217;http://a3.twimg.com/profile\_images/110763033/jquery\_normal.gif&#8217;
      
> },
      
> &#8216;in\_reply\_to\_status\_id&#8217;:null,
      
> &#8216;id&#8217;:4058535256,
      
> &#8216;favorited&#8217;:false,
      
> &#8216;text&#8217;:&#8217;jQuery Tip: Don&#8217;t forget that you can load jQuery UI files from Google servers as well http://bit.ly/fJs2r&#8217;
    
> },
    
> {
      
> &#8216;in\_reply\_to\_user\_id&#8217;:null,
      
> &#8216;in\_reply\_to\_screen\_name&#8217;:null,
      
> &#8216;source&#8217;:&#8217;web&#8217;,
      
> &#8216;created_at&#8217;:&#8217;Thu Sep 17 05:44:30 +0000 2009&#8242;,
      
> &#8216;truncated&#8217;:false,
      
> &#8216;user&#8217;:{
        
> &#8216;profile\_sidebar\_fill_color&#8217;:&#8217;e0ff92&#8242;,
        
> &#8216;description&#8217;:&#8217;jQuery and JavaScript howtos, tutorials, hacks, tips and performanace tests. Ask your jQuery questions here&#8230;&#8217;,
        
> &#8216;friends_count&#8217;:1015,
        
> &#8216;url&#8217;:&#8217;http://jquery-howto.blogspot.com&#8217;,
        
> &#8216;screen_name&#8217;:&#8217;jQueryHowto&#8217;,
        
> &#8216;following&#8217;:true,
        
> &#8216;profile\_sidebar\_border_color&#8217;:&#8217;87bc44&#8242;,
        
> &#8216;favourites_count&#8217;:15,
        
> &#8216;created_at&#8217;:&#8217;Thu Mar 26 14:58:19 +0000 2009&#8242;,
        
> &#8216;profile\_text\_color&#8217;:&#8217;000000&#8242;,
        
> &#8216;profile\_background\_image_url&#8217;:&#8217;http://s.twimg.com/a/1253048135/images/themes/theme1/bg.png&#8217;,
        
> &#8216;profile\_link\_color&#8217;:&#8217;0000ff&#8217;,&#8217;protected&#8217;:false,
        
> &#8216;verified&#8217;:false,
        
> &#8216;statuses_count&#8217;:503,
        
> &#8216;profile\_background\_tile&#8217;:false,
        
> &#8216;location&#8217;:&#8221;,
        
> &#8216;name&#8217;:&#8217;jQuery HowTo&#8217;,
        
> &#8216;profile\_background\_color&#8217;:&#8217;9ae4e8&#8242;,
        
> &#8216;id&#8217;:26767000,
        
> &#8216;notifications&#8217;:false,
        
> &#8216;time_zone&#8217;:&#8217;Central Time (US & Canada)&#8217;,
        
> &#8216;utc_offset&#8217;:-21600,
        
> &#8216;followers_count&#8217;:2035,
        
> &#8216;profile\_image\_url&#8217;:&#8217;http://a3.twimg.com/profile\_images/110763033/jquery\_normal.gif&#8217;
      
> },
      
> &#8216;in\_reply\_to\_status\_id&#8217;:null,
      
> &#8216;id&#8217;:4048429737,
      
> &#8216;favorited&#8217;:false,
      
> &#8216;text&#8217;:&#8217;Added a demo page for my &#8216;How to bind events to AJAX loaded elements&#8217; blog post as requested by users http://bit.ly/q2tWe&#8217;
    
> }
  
> ]

So I guess you can think of plenty of ways to use this.

 [1]: http://twittercounter.com
 [2]: http://www.lessthandot.com/statistics.php
 [3]: http://jquery-howto.blogspot.com/2009/11/jquery-twitter-plugin-update-jtwitter.html
 [4]: http://plugins.jquery.com/project/jtwitter