---
title: How I used Data Explorer to create a demo
author: Koen Verbeeck
type: post
date: 2013-03-20T10:22:00+00:00
ID: 2042
excerpt: In this short blog post I show how you can use Data Explorer to quickly gather some datasets for a demo.
url: /index.php/webdev/business-intelligence/how-i-used-data-explorer/
views:
  - 30682
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Excel Reporting
tags:
  - data explorer
  - excel
  - power view
  - powerpivot
  - self-service bi
  - syndicated

---
Some time ago [Data Explorer][1], an add-in for Excel 2010 or 2013, was released in Public Preview. In short, it is a self-service ETL tool which is in my opinion a great tool for quickly searching data sets.

Recently, I had to create a demo for showcasing PowerPivot and Power View in Office 2013. As with all demo's, the hardest part is to figure out which compelling story you will tell. I had downloaded a publicly available Excel file with Olympic data from 1900 until 2008.

<a style="text-align: center;" href="/media/users/koenverbeeck/DataExplorerDemo/OlympicsData.png?mtime=1363781218"><img src="/wp-content/uploads/users/koenverbeeck/DataExplorerDemo/OlympicsData.png?mtime=1363781218" alt="" width="668" height="234" /></a>

<span style="text-align: justify;">This is very useful for a demo, but I wanted to create some maps in Power View, so I also needed a list of countries and their location. And this is where Data Explorer came into the picture. One of the niftiest features of the product is to search online for datasets. In reality a lot of results come from Wikipedia, but we're not picky.</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DataExplorerDemo/onlinesearch.png?mtime=1363781225"><img src="/wp-content/uploads/users/koenverbeeck/DataExplorerDemo/onlinesearch.png?mtime=1363781225" alt="" width="598" height="194" /></a>
</div>

<span style="text-align: justify;">As you can see in the first screenshot, countries are identified by their NOC code (National Olympic Committee) so the first thing we need to do is find a list of countries and the corresponding NOC codes. To make future matching a bit easier, I also include ISO codes.</span>

<a style="text-align: center;" href="/media/users/koenverbeeck/DataExplorerDemo/searchresult.png?mtime=1363781237"><img src="/wp-content/uploads/users/koenverbeeck/DataExplorerDemo/searchresult.png?mtime=1363781237" alt="" width="895" height="213" /></a>

<span style="text-align: justify;">With one click on the USE button, the entire dataset is imported into a new Excel sheet:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DataExplorerDemo/importquery.png?mtime=1363781211"><img src="/wp-content/uploads/users/koenverbeeck/DataExplorerDemo/importquery.png?mtime=1363781211" alt="" width="604" height="200" /></a>
</div>

<span style="text-align: justify;">Next I import similar data sets containing list of countries with their location and also some lists with unemployment rates, population and gross national income. All I need to do some serious analyses on what are the key contributing factors in winning a gold medal at the Olympics.</span>

After I imported everything into PowerPivot, I did some rudimentary data cleansing. The hardest part was to match countries from different lists. For example, is it North Korea or Democratic People's Republic of Korea? Finally I have the following model:

<a style="text-align: center;" href="/media/users/koenverbeeck/DataExplorerDemo/diagram.png?mtime=1363781203"><img src="/wp-content/uploads/users/koenverbeeck/DataExplorerDemo/diagram.png?mtime=1363781203" alt="" width="722" height="380" /></a>

<span style="text-align: justify;">Now I can build some nice Power View reports directly in Excel 2013 and my demo is ready:</span>

<a style="text-align: center;" href="/media/users/koenverbeeck/DataExplorerDemo/powerview.png?mtime=1363781231"><img src="/wp-content/uploads/users/koenverbeeck/DataExplorerDemo/powerview.png?mtime=1363781231" alt="" width="732" height="394" /></a>

<span style="text-align: justify;">The point of this blog post is that it's fairly easy to create a demo data set using Data Explorer; it took me a bit more than one hour to create this model. However, Data Explorer is more than just an online search tool for data sets, so if you want to learn more about this tool check out the following resources:</span>

<p style="text-align: left;">
  <a href="http://sqlblog.com/blogs/jamie_thomson/archive/2013/02/27/data-explorer-hits-full-preview.aspx">Data Explorer hits full preview<br /></a><a href="http://www.mattmasson.com/2013/02/exploring-data-explorer/">Exploring Data Explorer<br /></a><a href="http://www.mattmasson.com/2013/03/access-the-windows-azure-marketplace-from-data-explorer/">Access the Windows Azure Marketplace from Data Explorer<br /></a><a href="http://denglishbi.wordpress.com/2013/03/04/installing-data-explorer-preview-demo-with-imdb-data/">Installing Data Explorer Preview & Demo with IMDB Data<br /></a><a href="http://cwebbbi.wordpress.com/2013/03/04/calling-a-web-service-from-data-explorer-part-1/">Calling A Web Service From Data Explorer, Part 1<br /></a><a href="http://cwebbbi.wordpress.com/2013/03/15/finding-shakespeares-favourite-words-with-data-explorer/">Finding Shakespeare's Favourite Words With Data Explorer</a>
</p>

 [1]: http://www.microsoft.com/en-us/download/details.aspx?id=36803