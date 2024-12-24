---
title: 'Friday SQL Nugget #1'
author: Jes Borland
type: post
date: 2012-01-13T17:34:00+00:00
ID: 1491
excerpt: |
  It's time for SQL Nuggets! This week's nugget is "Deciding I need to delete it all and start over again".
url: /index.php/datamgmt/datadesign/friday-sql-nugget-2/
views:
  - 3332
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming

---
 <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/sqlnugget.jpg?mtime=1326466147" alt="" title="" align="left" width="200" height="200" />It's time for [SQL Nuggets][1]! This is a cool idea started by LTD's own [Ted Krueger][2] – a quick SQL-related blog on Fridays. This week's nugget is **"Deciding I need to delete it all and start over again"** (and I'd like that with a side of honey mustard, please). 

A wise Jedi once said, "Try not. Do or do not. There is no try." What happens when you do, but you do poorly? You do it over. 

I'm not going to focus on a report I built, or code I wrote. I'm going to talk about a challenge at my (still-new-to-me) job: database design. 

Database design is part art, part war. There are many, many blogs, articles, and books on how to design a database. How to gather requirements. What questions to ask the users. What I wasn't prepared for was: how to capture that information. 

My first design project started simply enough. I was going through requirements documents and talking to the project manager. I grabbed a stack of Post-It Notes and started scribbling entities on them. I thought, "I'll put them in something later." What started as a few Post-Its quickly grew to a wall full of them. Attributes were scratched out, then added again. I had no way to capture data types, or notes about specific fields I knew I'd need. 

Simply put, it was a mess. 

I decided to throw them all away and start over. Hours of work, thrown away? Yes. It was making more work for me to have to refer to these little pieces of paper over and over. I couldn't search items. I couldn't find relationships. When trying to talk through an issue with the project manager, she couldn't read my handwriting. 

So, I downloaded Toad Data Modeler, opened a logical model workspace, and started all over again. Was it worth it? Absolutely. While writing it out on paper was a good short-term solution, it was just that: short-term. I didn't think about scaling it out, searching things, or relationships. It was a good lesson to learn, even if I did end up duplicating some work. 

Who would I like to hear from? I'm tagging my Tech On Tap co-conspirators, Mark Cyrulik, Derek Schauland, and Grant Fritchey for this challenge.

 [1]: /index.php/ITProfessionals/ProfessionalDevelopment/friday-sql-nugget-1
 [2]: /index.php/All/?disp=authdir&author=68