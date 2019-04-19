---
title: The WordPress Migration You Probably Didn't Know About
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-01-22T16:15:15+00:00
ID: 2271
url: /index.php/webdev/the-wordpress-migration-you-probably-didnt-know-about/
views:
  - 59528
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - b2evolution
  - wordpress

---
About two weeks ago, LessThanDot completely switched blog engines from B2Evolution to WordPress. We did this in the middle of the day on a Sunday, with a minimum of stress or downtime and without any noticeable complaints from our 50-60,000 monthly visitors.

Read on for details on the migration, why we chose WordPress, and some of the positives and negatives we have found so far.

## The Migration

My philosophy on migrations is to make them as seamless as possible, so that wherever possible users just don't notice that you have pulled a website out from under them and replaced it with a new one.

LessThanDot is composed of 4 major components: the blogs (with over 2000 posts!), the wiki, the forums, and the middle bits that hold everything together and provide unified accounts (and then some more bits around the edges like SQLCop and Cooking). 

[<img src="/wp-content/uploads/2014/01/LTDUmbrella1.png" alt="LTDUmbrella" width="553" height="148" class="aligncenter size-full wp-image-2300" srcset="/wp-content/uploads/2014/01/LTDUmbrella1.png 553w, /wp-content/uploads/2014/01/LTDUmbrella1-300x80.png 300w" sizes="(max-width: 553px) 100vw, 553px" />][1]

When I decided to finally bite the bullet and replace the blog engine (by popular request), I had three audiences, each with their own set of goals:

**Readers (y'all)**

  * Keep all the existing posts we find on google, bookmark and send to co-workers, etc
  * Keep all the comments we have left over time
  * Make sure none of the pictures disappear, especially the once that have stats and other critical bits of info in them
  * Minimize downtime
  * Minimize shocking changes to the layout, availability of search, etc

**Writers (A smaller set of y'all)**

  * Preserve everyone's hard work, including 2+ year old drafts that might still get published some day
  * Make it easier to create, schedule, and publish new posts (or in some cases, just less cluttered or error-prone)
  * Make things like Google authorship and embedding micro-data in posts easy (or totally magically behind the scenes)
  * Support 3rd-party blog writing applications (this is the one we haven't gotten done, argh)

**Developers (the smallest set)**

  * The ability to be able to make theme changes easily, with few or no arcane voodoo steps
  * Simpler environment for plugins and integrating the blogs with the rest of our site (I still don't get the b2evo folder and file structure)
  * Simplify upgrades for the core software and plugins so we can stay on top of them easily
  * Provide the ability to do neat things like Google authorship without requiring some form of voodoo and several weekends of hackery
  * Make the writers happy so they'll let us stay in the pool longer at the annual LessThanDot Summits 🙂

And I think we have been successful.

## WordPress Observations, the Positive

I'm pretty sure someone else did the original LessThanDot teeming work on B2Evolution but I created the new new layout theme for B2Evo that we have sported since 2009. I remember having to do a lot of guessing and print statements to try and figure out what files did what in the templates, and the documentation was only of limited helpfulness.

### I Just Built It...

I started out the WordPress migration with a blank theme folder called “LessThanDot2009”. There are a plethora of posts out there on creating a custom theme, but the one I liked walked through the creation of a theme in small steps, building it up one piece at a time. I followed along, using LessThanDot code snippets where the author was using bootstrap, until before I knew it I had most of our site put together. 

<div id="attachment_2282" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/01/WordpressProgress.png"><img src="/wp-content/uploads/2014/01/WordpressProgress-300x187.png" alt="WordPress Migration - The First Page" width="300" height="187" class="size-medium wp-image-2282" srcset="/wp-content/uploads/2014/01/WordpressProgress-300x187.png 300w, /wp-content/uploads/2014/01/WordpressProgress.png 800w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    WordPress Migration – The First Page
  </p>
</div>

I quickly started branching out as I followed the tutorial, and before long I realized that Googling for “WordPress _anything_” would consistently net me a clear set of documentation pages with the exact the functions or hooks that I needed to do that _anything_.

<div style="float: right; background-color: #dddddd; margin: 4px 0px 4px 8px; padding: 12px 8px 12px 0;">
  <h4 style="margin: 0px 0px 8px 8px;">
    Those Helpful Links
  </h4>

<ul>
<li>
<a href="http://blog.teamtreehouse.com/responsive-wordpress-bootstrap-theme-tutorial">How to Build a Responsive WordPress Theme with Bootstrap</li> 

<li>
  <a href="https://codex.wordpress.org/Function_Reference/get_permalink">WordPress Documentation for get_permalink</a>
</li>
<li>
  <a href="http://yoast.com/wordpress-theme-anatomy/">The anatomy of a WordPress theme</a>
</li></ul> </div> 

<p>
  When I branched out from that initial tutorial and realized I needed less generic listing pages to display author posts, for instance, there was a great infographic that showed me exactly how WordPress mapped theme files to incoming requests. The whole process was a smooth flow, from empty folder to mostly functional duplicate theme in WordPress.
</p>

<p>
  The ease with which the layout went together was great. As I knocked this out I was able to start identifying potential plugins to socket in and provide behavior that wasn't built-in, such as view counts and code highlighting.
</p>

<h3>
  Getting More Advanced, Integration
</h3>

<p>
  Where things got really messy in some other packages (*cough* MediaWiki) was around the custom authentication and the cross-package integrations. The authentication that ties all of our packages into unified accounts, while the integration allows us to do things like expose data from the blogs on the front page of the site and the statistics area.
</p>

<p>
  <a href="/wp-content/uploads/2014/01/login.png"><img src="/wp-content/uploads/2014/01/login.png" alt="login" width="439" height="229" class="aligncenter size-full wp-image-2295" srcset="/wp-content/uploads/2014/01/login.png 439w, /wp-content/uploads/2014/01/login-300x156.png 300w" sizes="(max-width: 439px) 100vw, 439px" /></a>
</p>

<p>
  The authentication plugin was easy, with the only messy bits being in my own hackery all of the packages had to interact with (I wrote the central part, all that ugliness is my fault). WordPress clearly defined the hooks I needed to attach into and numerously numerous posts outlined how others had written custom authentication plugins. Even when I accidentally locked out all the native WordPress accounts without having an LTD ones setup yet, it was easy to get unstuck with just a few internet searches.
</p>

<p>
  Next up was data integration and showing posts on the front page of LessThanDot.com...
</p>

<p>
  In the past, Chrissie had built some nice DAL objects with interfaces to pull data out of the various packages and bring it together on the front page. I could have just swapped out the SQL statements, but before I did that I decided to see if anyone else in the vast WordPress blogosphere had cracked this nut in the past, since I already had some nice, fresh templates I had built.
</p>

<p>
  Well what do you know: <a href="http://codex.wordpress.org/Integrating_Wordpress_with_Your_Website">Integrating WordPress with Your Website</a>.
</p>

<p>
  What this helpful WordPress document outlines is a method to include WordPress into another portion of your site, allowing you to use all of the native logic for pulling in the posts and displaying them. basically the front page of our site ended up amounting to pulling in WordPress and telling it to show the post listing template. I basically went from showing B2Evo content to WordPress content in about 5 minutes.
</p>

<p>
  Woo!
</p>

<p>
  It all really moved this fast. As I had a few hours or a half day on a Saturday, I would sit down and just knock out a whole section of functionality (then take 3-4 weeks off to do other things). If this had been a real job, I'm confidant this would have taken less than a week...well, if you don't count importing.
</p>

<h3>
  Argh, importing...
</h3>

<p>
  The hard part ended up being the data. We had over 2000 posts we wanted to bring across, blog authors we wanted to preserve, thousands of custom tags, way too many categories, more thousands of comments, view counts I wanted to preserve, images in posts that needed their URL redirected to wherever I ended up copying the b2evo image folders, ... surely this had been solved before.
</p>

<p>
  We found an importer, and paid for it. Unfortunately the importer had a number of basic things it wasn't doing correctly and because the code was encrypted, I couldn't fix it. So after numerous test runs and slowly getting ever more frustrated, I threw it out and wrote my own.
</p>

<div id="attachment_2285" style="width: 682px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/01/WordpressUnicode.png"><img src="/wp-content/uploads/2014/01/WordpressUnicode.png" alt="B2Evo had some mix of latin, unicode, and MS Word" width="672" height="192" class="size-full wp-image-2285" srcset="/wp-content/uploads/2014/01/WordpressUnicode.png 672w, /wp-content/uploads/2014/01/WordpressUnicode-300x85.png 300w" sizes="(max-width: 672px) 100vw, 672px" /></a>
  
  <p class="wp-caption-text">
    B2Evo had some mix of latin, unicode, and MS Word
  </p>
</div>

<p>
  This turned out to be the best decision, as it let me slowly add in special processing rules to clean up image paths, only importing initial users that had posted blog posts, push view counts into the meta data table just right to be picked up by the view count plugin, and eventually a bunch of logic to map code blocks from the B2Evo posts to a format that would be understood by the syntax highlighting plugin I had selected for WordPress.
</p>

<div id="attachment_2287" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/01/wordpress_import.png"><img src="/wp-content/uploads/2014/01/wordpress_import-300x100.png" alt="Importing into WordPress" width="300" height="100" class="size-medium wp-image-2287" srcset="/wp-content/uploads/2014/01/wordpress_import-300x100.png 300w, /wp-content/uploads/2014/01/wordpress_import.png 728w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Importing into WordPress
  </p>
</div>

<p>
  Don't get me wrong, if someone else had written something like this and made it available, I totally would have started with theirs. In a later blog post, I'll post up the code for this plugin in case anyone else finds it helpful down the road (and because I'm still annoyed by the one we paid for).
</p>

<h2>
  Ok, so enough happy-fluffy-puppy-stuff, what sucked?
</h2>

<p>
  There have been a few negatives to the WordPress move, but they were less negatives and more opportunities for WordPress to get even better.
</p>

<p>
  1. The syntax seemed relatively consistent, or at least well documented enough that I didn't notice when it was inconsistent. But it could have been even more obvious/consistent which functions were returning values and which were going to echo.
</p>

<p>
  2. What functions are calling the database? Every time I add a function outside “The Loop”, I'm worried that I'm causing more database calls to be made when I don't want them to be. It's not clear when it will be smart enough to have 5 or 6 individual calls not call the database on their own when the whole post is going to be loaded just a bit further down the page? I haven't done a lot of digging on this topic, but it seems to be more heavily technical than most people want to read about on WordPress, so it doesn't show up in the documentation.
</p>

<p>
  which brings me to
</p>

<p>
  3. A lot of WordPress content is for the beginning developer. And a lot of the PHP examples have grossly horrible syntax that is bad even by my standards.
</p>

<p>
  Example:
</p>

```php
<?php
require('/the/path/to/your/wp-blog-header.php');
?>

<?php
$posts = get_posts('numberposts=10&#038;order=ASC&#038;orderby=post_title');
foreach ($posts as $post) : setup_postdata( $post ); ?>


<?php the_date(); echo "<br />"; ?>


<?php the_title(); ?>    


<?php the_excerpt(); ?> 


<?php
endforeach;
?>
```

<p>
  WTF ??
</p>

<p>
  No really, W.T.F. ?!?
</p>

<p>
  This was just the very first example I found, not even one of the rally bad ones.
</p>

<p>
  4. Look at the source code for plugins before you use them so you know which ones have a lot of positive comments but are obviously going to be impossible to support long term. Some of those same beginner developers got a plugin running on their machine, but you know in a version or two it's going to be the biggest security leak in your site.
</p>

<p>
  5. The Spam!
</p>

<div id="attachment_2289" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/01/wordpress-spam.png"><img src="/wp-content/uploads/2014/01/wordpress-spam-300x55.png" alt="Time to empty out the spam for the day..." width="300" height="55" class="size-medium wp-image-2289" srcset="/wp-content/uploads/2014/01/wordpress-spam-300x55.png 300w, /wp-content/uploads/2014/01/wordpress-spam.png 363w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Time to empty out the spam for the day...
  </p>
</div>

<p>
  Oh my god the spam. We have built up our spam rules on B2Evo over the course of years without even realising how much spam LessThanDot would be getting without those rules. Within the first 8 minutes of switching the sub-domain to show WordPress instead of B2Evo, we received more than 10 spam comments. Chrissie started deleting, I started deleting, a vast array of spammers kept at it, I started banning IPs...and the flood kept coming at a steady pace of 2 spam comments/minute. I finally found a good spam plugin the next day that has managed the 2/minute load just fine without any performance impact on our servers (we still have to empty out the spam folder occasionally though, oh well).
</p>

<p>
  Those were the bad moments though, everything else was smooth and easy.
</p>

<h2>
  Conclusion
</h2>

<p>
  Overall, the move to WordPress seems to have been a good one. WordPress as a platform has a heavy focus on customization and extendability, with clear documentation on the routines and hooks available and a huge community of theme and plugin authors. Though I found their support forums more frustrating than helpful, I often was able to find the information I needed elsewhere on the internet. The focus of the platform helped make our migration easier, and I think will provide a good platform for future changes as we continue to evolve the site and how we produce content.
</p>

 [1]: /wp-content/uploads/2014/01/LTDUmbrella1.png