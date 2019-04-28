---
title: I know you can do better than that
author: Christiaan Baes (chrissie1)
type: post
date: 2010-12-28T08:44:11+00:00
ID: 984
excerpt: |
  Today I was going through some code that we use for this site and found this.
  
  if( $this-&gt;disp_params['show_recently'] )
          {
              echo $this-&gt;disp_params['item_start'];
              echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get(&hellip;
url: /index.php/itprofessionals/ethicsit/i-know-you-can-do-better-than-that/
views:
  - 1355
categories:
  - Ethics and IT
tags:
  - php

---
Today I was going through some code that we use for this site and found this.

```php
if( $this-&gt;disp_params['show_recently'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('url').'"&gt;'.T_('Recently').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_search'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('searchurl').'"&gt;'.T_('Search').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_postidx'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('postidxurl').'"&gt;'.T_('Post index').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_archives'] )
        {
            // fp&gt; TODO: don't display this if archives plugin not installed... or depluginize archives (I'm not sure)
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('arcdirurl').'"&gt;'.T_('Archives').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_categories'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('catdirurl').'"&gt;'.T_('Categories').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_mediaidx'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('mediaidxurl').'"&gt;'.T_('Photo index').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_latestcomments'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('lastcommentsurl').'"&gt;'.T_('Latest comments').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_owneruserinfo'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('userurl').'"&gt;'.T_('Owner details').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_ownercontact'] && $url = $Blog-&gt;get_contact_url( true ) )
        {   // owner allows contact:
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$url.'"&gt;'.T_('Contact').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_sitemap'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;strong&gt;&lt;a href="'.$Blog-&gt;get('sitemapurl').'"&gt;'.T_('Site map').'&lt;/a&gt;&lt;/strong&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
```
And what I needed was this.

```php
if( $this-&gt;disp_params['show_recently'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('url').'"&gt;'.T_('Recently').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_search'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('searchurl').'"&gt;'.T_('Search').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_postidx'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('postidxurl').'"&gt;'.T_('Post index').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_archives'] )
        {
            // fp&gt; TODO: don't display this if archives plugin not installed... or depluginize archives (I'm not sure)
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('arcdirurl').'"&gt;'.T_('Archives').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_categories'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('catdirurl').'"&gt;'.T_('Categories').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_mediaidx'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('mediaidxurl').'"&gt;'.T_('Photo index').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_latestcomments'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('lastcommentsurl').'"&gt;'.T_('Latest comments').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_owneruserinfo'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('userurl').'"&gt;'.T_('Owner details').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_ownercontact'] && $url = $Blog-&gt;get_contact_url( true ) )
        {   // owner allows contact:
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$url.'"&gt;'.T_('Contact').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
 
        if( $this-&gt;disp_params['show_sitemap'] )
        {
            echo $this-&gt;disp_params['item_start'];
            echo '&lt;a href="'.$Blog-&gt;get('sitemapurl').'"&gt;'.T_('Site map').'&lt;/a&gt;';
            echo $this-&gt;disp_params['item_end'];
        }
```
That&#8217;s right, I just had to remove all the strong-tags.

And I was like, really? Did the person that wrote that not think before writting, was he not lazy enough? Was he to lazy to write an extra method and make it eassier to maintain? 

Perhaps the person that wrote this thought it did the job and moved on, and he was right. But he did not think someone else was going to read and have to maintain his code. 

So next time you write something like this, remember this. I will come after you and find you and slowly kill you. Remember me.

BTW coding for reuse in mind is always a good thing untill you discover that your reuse libraries have become bigger than the application you are going to need it in and that application will only use 5% of the library, thus adding bloat to your application.

It is always difficult to know when to write for reuse (being DRY) and when to give up and just do some things twice. I guess experience is the only way how you can learn such things. The biggest problem is that you don&#8217;t know what the future will bring. Because if a user says they ain&#8217;t gonna need it today, he might just change his mind &#8230; 10 minutes later.