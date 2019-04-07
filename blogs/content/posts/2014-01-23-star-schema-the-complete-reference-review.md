---
title: Star Schema The Complete Reference – Review
author: Koen Verbeeck
type: post
date: 2014-01-23T15:02:48+00:00
ID: 2315
url: /index.php/datamgmt/datadesign/star-schema-the-complete-reference-review/
views:
  - 17858
rp4wp_auto_linked:
  - 1
categories:
  - Book Review
  - Data Modelling and Design
tags:
  - dimensional modelling
  - review
  - star schema
  - syndicated

---
<p style="text-align: justify">
  I finished my first book of this year and here’s the review! After I finished the newest edition of Ralph Kimball’s Data Warehouse Toolkit, a senior colleague of mine recommended the book <a href="http://www.amazon.com/Star-Schema-The-Complete-Reference/dp/0071744320">Star Schema The Complete Reference</a> by Christopher Adamson. To be honest, I had never heard about it. But was I wrong: this book is an absolute must read for every data warehouse professional who takes himself/herself seriously. The book is extremely well written and it explains the concepts of star schemas (hence the title of course) very thoroughly with great detailed examples. I would even go as far by proclaiming that I like this book better than the “Toolkit”. If I had to choose only one book to take with me on new projects, it would be this one.
</p>

<p style="text-align: justify">
  One of the strengths of this book is that it is vendor independent and also architecture independent. The book begins by introducing how dimensional modelling is used in the methodologies used by Inmon and Kimball and it debunks quite a few myths that exist about both methodologies. It also explains the concept of stand-alone data marts and which advantages and especially which disadvantages this technique has. Throughout the book, the author explains how the different methodologies can have an impact on how certain concepts are implemented.
</p>

<p style="text-align: justify">
  Another great piece was the explanation of drilling-across and the importance of it for cross analysis of different process. While reading Kimball’s book, I didn’t pick up the significance of this, but because of the attention Christopher gives to conformed dimensions – a whole chapter – I could really put it in perspective. The different types of conformity are well explained and are illustrated with clear examples. The book also gives different ways of drilling across and provides a few code samples.
</p>

[<img class="alignnone wp-image-2317 size-medium" src="/wp-content/uploads/2014/01/bookcover-241x300.jpg" alt="bookcover" width="241" height="300" srcset="/wp-content/uploads/2014/01/bookcover-241x300.jpg 241w, /wp-content/uploads/2014/01/bookcover.jpg 322w" sizes="(max-width: 241px) 100vw, 241px" />][1]

<p style="text-align: justify">
  There are the usual chapters that you expect from a book like this: dimension design, slowly changing attributes, snowflaking, different kinds of fact tables et cetera. But there is the occasional deep dive: multi-valued dimensions/ attributes and how they relate to bridge tables, type-specific stars, recursive hierarchies and so on. He even explains how to handle all these exotic features when there are slowly changing attributes, but unfortunately he doesn’t back it up with detailed examples. It’s the only weak point of the book, but making the book too detailed would perhaps make it too long and harder to read. And hopefully we never have to implement a slowly changing many to many relationship.
</p>

<p style="text-align: justify">
  There are also some chapters on performance enhancements, such as derived tables and aggregates. These solutions might become somewhat obsolete in the past years thanks to hardware advancements and the introduction of columnar and in-memory databases, but since not every company has Enterprise edition these tools might still be very useful.
</p>

<p style="text-align: justify">
  The last three chapters are more about how to design the ETL, how to work with BI tools and how to document models. Personally I found those less interesting, but there’s still some value in it.
</p>

<p style="text-align: justify">
  <b>Conclusion</b>
</p>

<p style="text-align: justify">
  It’s a great book about designing data warehouses using star schemas. Buy it, read it and take it with you on every data warehouse project you encounter. You won’t regret it.
</p>

 [1]: http://amzn.to/1fJVq5q