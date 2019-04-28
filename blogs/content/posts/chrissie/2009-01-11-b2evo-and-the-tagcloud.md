---
title: B2evo and the tagcloud
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-11T08:44:34+00:00
ID: 280
url: /index.php/webdev/webdesigngraphicsstyling/b2evo-and-the-tagcloud/
views:
  - 3296
categories:
  - Web Design, Graphics and Styling

---
We here at LessThanDot use [b2evo][1] as our blogengine for several reasons. 

1) It&#8217;s written in php and therefor fits well with the other parts of the site.
  
2) It&#8217;s free.
  
3) It&#8217;s open source.
  
4) It&#8217;s free.
  
5) It supports multiple authors.
  
6) It supports cattegories.

The categories were very important because we wanted to create the same structure on the blog that we had on the forums. We started of with the idea of creating a technical forum. And we soon decided that a wiki and blog would be a great addition to the forum. So we wanted a wiki and blog that could have the same sections as the forum. [b2evo][1] has all that we wanted. 

So b2evo was perfect for us, nearly the only one we could find at that time. We would also like to thank [François Planque][2] for making it all happen. BTW I&#8217;m not saying b2evo is perfect in every way.

Now let&#8217;s jump forward to the tagcloud. Soon after launching mr. SQLDenis thought it would be a good idea to have not only the categories but also tags. b2evo supports both so setting it up is easy. Just add tags to your posts and add the tagcloud as a widget. A few weeks ago we upgraded to 2.4.5 of b2evo and this weekend 😳 (a bit late I know) I noticed that the tagcloud wasn&#8217;t there on the front page. The front page uses the AllBlogs blog for this to show. The Allblogs is a separate blog/main category but in actual fact it doesn&#8217;t contain any articles. An article always belongs to one of the other main categories never all blogs. So I went out to look for the code of the tagcloud to see what could be wrong. And I found it under inc/widgets/widgets/\_coll\_tag_cloud.widget.php. 

And I found this piece of code. 

```php
$sql = 'SELECT LOWER(tag_name) AS tag_name, COUNT(DISTINCT itag_itm_ID) AS tag_count
                          FROM T_items__tag INNER JOIN T_items__itemtag ON itag_tag_ID = tag_ID
                                    INNER JOIN T_postcats ON itag_itm_ID = postcat_post_ID
                                    INNER JOIN T_categories ON postcat_cat_ID = cat_ID
                                    INNER JOIN T_items__item ON itag_itm_ID = post_ID
                         WHERE cat_blog_ID = '.$Blog-&gt;ID.'
                          AND post_status = "published" AND post_datestart &lt; NOW()
                         GROUP BY tag_name
                         ORDER BY tag_count DESC
                         LIMIT '.$this-&gt;disp_params['max_tags'];```
It uses the $Blog->ID to select the current blog and then select the articles which is OK for all the real main categories but that doesn&#8217;t work for the all blogs category. So I changed it to this not so perfect version.

```php
if($Blog-&gt;ID == theallblogid)
{
            $sql = 'SELECT LOWER(tag_name) AS tag_name, COUNT(DISTINCT itag_itm_ID) AS tag_count
                          FROM T_items__tag INNER JOIN T_items__itemtag ON itag_tag_ID = tag_ID
                                    INNER JOIN T_postcats ON itag_itm_ID = postcat_post_ID
                                    INNER JOIN T_categories ON postcat_cat_ID = cat_ID
                                    INNER JOIN T_items__item ON itag_itm_ID = post_ID
                         WHERE post_status = "published" AND post_datestart &lt; NOW()
                         GROUP BY tag_name
                         ORDER BY tag_count DESC
                         LIMIT '.$this-&gt;disp_params['max_tags'];
}
else
{
        $sql = 'SELECT LOWER(tag_name) AS tag_name, COUNT(DISTINCT itag_itm_ID) AS tag_count
                          FROM T_items__tag INNER JOIN T_items__itemtag ON itag_tag_ID = tag_ID
                                    INNER JOIN T_postcats ON itag_itm_ID = postcat_post_ID
                                    INNER JOIN T_categories ON postcat_cat_ID = cat_ID
                                    INNER JOIN T_items__item ON itag_itm_ID = post_ID
                         WHERE cat_blog_ID = '.$Blog-&gt;ID.'
                          AND post_status = "published" AND post_datestart &lt; NOW()
                         GROUP BY tag_name
                         ORDER BY tag_count DESC
                         LIMIT '.$this-&gt;disp_params['max_tags'];
}```
Where I changed theallblogid with our actual id. Simple change to make it work.

<span class="MT_red">EDIT:</span> Well, I posted this on the b2evo forum aswell and there someone with a difficult handle (¥åßßå) posted another solution. [And here it is][3].

```php
$blog_list = ( $Blog-&gt;get_setting( 'aggregate_coll_IDs' ) ? $Blog-&gt;get_setting( 'aggregate_coll_IDs' ) : $Blog-&gt;ID );

$sql = 'SELECT LOWER(tag_name) AS tag_name, COUNT(DISTINCT itag_itm_ID) AS tag_count
                          FROM T_items__tag INNER JOIN T_items__itemtag ON itag_tag_ID = tag_ID
                                    INNER JOIN T_postcats ON itag_itm_ID = postcat_post_ID
                                    INNER JOIN T_categories ON postcat_cat_ID = cat_ID
                                    INNER JOIN T_items__item ON itag_itm_ID = post_ID
                         WHERE cat_blog_ID IN ( '.$blog_list.' )
                          AND post_status = "published" AND post_datestart &lt; NOW()
                         GROUP BY tag_name
                         ORDER BY tag_count DESC
                         LIMIT '.$this-&gt;disp_params['max_tags'];```
A lot shorter then mine. Thanks for that.

 [1]: http://b2evolution.net/
 [2]: http://fplanque.net/
 [3]: http://forums.b2evolution.net/viewtopic.php?p=85405#85405