---
title: Six (and a bit) years of LessThanDot, Visualized
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-05-05T16:34:13+00:00
ID: 2601
url: /index.php/datamgmt/datadesign/six-and-a-bit-years-of-lessthandot-visualized/
featured_image: /wp-content/uploads/2014/05/blogs_image.png
views:
  - 6781
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
I ran into a tool named [Gource][1] while searching for ways to visualize activity from my git repositories. Besides creating awesome visualizations from my code, it also has the capability to take custom logs for visualizations.

Which led me to think, what if I fed in blog posts from LessThanDot, using the primary categories in place of directory paths?

{{< youtube WfEvuLY3nkQ >}}

The color-coding for my git repositories is assigned by file type, but here I assigned colors to each of the authors. The final view is what we have built over the past 6+ years, a beautiful collection of useful knowledge from 38 different authors.

## The Nitty Gritty Details

This is by no means a polished, automated process, but here is how I converted WordPress data into a custom log I could feed into Gource.

### 1. Query Posts from WordPress

First I queried MySQL for the post data:

```sql
SELECT 
   UNIX_TIMESTAMP(CASE WHEN P.post_date_gmt < '2001-1-1' THEN P.post_date ELSE P.post_date_gmt END) AS "timestamp",
   U.display_name, 
   (CASE post_type WHEN 'post' THEN 'A' WHEN 'revision' THEN 'M' ELSE post_type END) as "change", 
   P.post_title,
   TT.term_taxonomy_id
FROM wp_posts P
   INNER JOIN wp_users U ON P.post_author = U.ID
   INNER JOIN wp_term_relationships TR ON TR.object_id = P.ID
   INNER JOIN wp_term_taxonomy TT ON TT.term_taxonomy_id = TR.term_taxonomy_id
   LEFT JOIN wp_term_relationships TR2 ON TR2.object_id = P.ID AND TR2.term_taxonomy_id < TR.term_taxonomy_id
WHERE P.post_type IN ('post','revision')
   AND P.post_status <> 'auto-draft'
   AND post_title <> ''
   AND TT.taxonomy = 'category'
   AND TR2.term_taxonomy_id IS NULL
ORDER BY P.post_date
```
Note that I gathered both additions and revisions, filtering out only drafts. I did have a few posts without a valid GMT date, so I failed those over to the local post date. Because we assign multiple categories to posts, I needed to be careful to select only a single one for each post.

### 2. Query Categories from WordPress

Next I needed the categories (terms) from WordPress. 

```sql
SELECT 
   TT.term_taxonomy_id,
   TT.parent,
   T.Name,
   T.slug
FROM wp_term_taxonomy TT
   INNER JOIN wp_terms T ON T.term_id = TT.term_id
WHERE taxonomy = 'category'
```
I wasn't sure if I was going to use the name or slug in the “file path”, so I grabbed both. I also grabbed the parent so I could recursively build the paths in Excel.

### 3. Generate Category Paths in Excel

Before building out my final list of post data, I need to recursively build out the category taxonomy. My category query results above go into an excel tab, which I then add a column that calculates the category path:

`=IF($B1=0,$C1,VLOOKUP($B1,$A$1:$E$126,5) & "/" & $C1)`

If the category has a parent id of 0, we know it's a parent category and output it's name alone. Otherwise we VLOOKUP to find the name of the parent and prepend it to the current rows category name. This gives us a table of category paths to reference from our post data.

### 4. Assign Colors in Excel

I found a website that had a list of color codes and pasted it into a new excel tab. I then grabbed the column of authors from the posts query, remove duplicates, and pasted it in Column A of my colors sheet (the codes are in column D).

Nothing fancy, but this will let me use a VLOOKUP from the posts tab to produce color codes for each posts by author.

### 5. Build the log file in Excel

With the results of the first query in an excel tab named “wp\_posts” and the categories in “wp\_term\_taxonomy”, I add some extra rows in the wp\_posts sheet to accommodate calculated fields I will be adding:

A:unix timestamp, B:author, C:A/M, D:(inserted), E:(inserted), F:(inserted), G:post title, H:category/term id

In Column D, I add the following lookup to build the “File path”:

`="LessThanDot/" & VLOOKUP($H1,wp_term_taxonomy!$A$1:$E$126,5,FALSE) & "/" & G1`

This uses VLOOKUP to find the category “path”, prepends a root of “LessThanDot” and appends the post title, giving me something that looks like a file path.

In Column E, I do the VLOOKUP that grabs each authors assigned color:

`=VLOOKUP(B1,colors!$A$2:$D$39,4,FALSE)`

In Column F, I create a value that matches the format for Gource's [Custom Log Format][2]:

`=A1&"|"&B1&"|"&C1&"|"&D1&"|"&E1`

Copying that column into a text file gives me something like this:

```text
1198243476|Christiaan Baes (chrissie1)|A|LessThanDot/Web Developer/Server Programming/ASP.NET/The concepts of OOP|00AEEF
1202432676|SQLDenis|A|LessThanDot/Data Management/Data Modelling and Design/Review of Inside Microsoft SQL Server 2005 Query Tuning and Optimization|F9AD81
1204209656|Christiaan Baes (chrissie1)|A|LessThanDot/Desktop Developer/Microsoft Technologies/VB.NET/VS 2008 ErrorMessage|00AEEF
1204243512|Christiaan Baes (chrissie1)|A|LessThanDot/Desktop Developer/Microsoft Technologies/VB.NET/Listen to yourself|00AEEF
...
```
### Run Gource

I've done several runs with Gource this weekend, so I've been playing with various combinations of settings. For this video, I wanted to display one of our old LessThanDot footer logos in the bottom right of the visualization, I suppressed the progress bar from the video, and made various tweaks to the time settings to speed up (5.5 years of posts with the default settings would have been loooong). 1280&#215;720 matches YouTube's HD setting, and I tweaked the date format and color to make it stand out less.

`gource --logo site_logo_footer.png -1280x720 --logo-offset 30x30<br />
 --hide "progress" --font-colour 454545 --seconds-per-day .25 --auto-skip-seconds 1 -o gource.ppm --path posts.log --dat<br />
e-format "%B %d, %Y"`

I then used ffmpeg to convert that 95GB ppm file into a 3.2 GB mp4 and uploaded it to youtube.

`C:\ffmpeg\bin\ffmpeg -an -threads 4 -y -r 60 -f image2pipe -vcodec ppm -i gource.pp<br />
m -vcodec libx264 -preset ultrafast -pix_fmt yuv420p -crf 1 -threads 0 -bf 0 -r 30 gource_blogposts.x264.mp4`

And there we go, 6+ years of blog posts visualized.

 [1]: https://code.google.com/p/gource/
 [2]: http://code.google.com/p/gource/wiki/CustomLogFormat