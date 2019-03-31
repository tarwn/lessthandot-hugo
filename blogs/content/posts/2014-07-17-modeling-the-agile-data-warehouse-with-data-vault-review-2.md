---
title: Modeling the Agile Data Warehouse with Data Vault – Review
author: Koen Verbeeck
type: post
date: 2014-07-17T11:18:38+00:00
url: /index.php/datamgmt/datadesign/modeling-the-agile-data-warehouse-with-data-vault-review-2/
views:
  - 39148
rp4wp_auto_linked:
  - 1
categories:
  - Book Review
  - Business Intelligence
  - Data Modelling and Design
tags:
  - agile
  - data modeling
  - data vault
  - data warehouse
  - syndicated

---
<p style="text-align: justify">
  A few months ago I followed an introductory course about <a href="http://en.wikipedia.org/wiki/Data_Vault_Modeling">the data vault modeling</a> technique for data warehouses at a <a href="https://www.infosupport.com/">local training provider</a>. As part of the training, I received the book <a href="http://www.amazon.com/Modeling-Agile-Warehouse-Vault-Volume/dp/061572308X/ref=tmm_pap_title_0?ie=UTF8&qid=1405584375&sr=8-2">Modeling the Agile Data Warehouse with Data Vault</a> written by <a href="http://hanshultgren.wordpress.com/">Hans Hultgren</a> who also is an instructor for the official data vault certification. This blog post is a review of this book; I will not introduce the concepts of data vault modeling.
</p>

<p style="text-align: justify">
  <a href="http://blogs.ltd.local/wp-content/uploads/2014/07/datavault.jpg"><img class="alignnone wp-image-2841 size-medium" src="http://blogs.ltd.local/wp-content/uploads/2014/07/datavault-200x300.jpg" alt="datavault" width="200" height="300" srcset="http://blogs.ltd.local/wp-content/uploads/2014/07/datavault-200x300.jpg 200w, http://blogs.ltd.local/wp-content/uploads/2014/07/datavault.jpg 231w" sizes="(max-width: 200px) 100vw, 200px" /></a>
</p>

<p style="text-align: justify">
  First of all I am glad I received this book for “free” (read: it was most likely included in the price of the training), because the book has a list price of a whopping $95 (Amazon fortunately gives a steep discount on the paperback edition). No, the 434-page book is not made of pure gold, as you might think, but there are quite a lot of pages in color. There are 212 figures in color in the book, so I guess this contributes to the total price, but <a href="http://www.amazon.com/Information-Dashboard-Design-At---Glance/dp/1938377001/ref=sr_1_1?s=books&ie=UTF8&qid=1405585061&sr=1-1&keywords=information+dashboard+design&dpPl=1">Information Dashboard Design</a> is also in color and has a list price of $40, less than half of the data vault book. This is of course only relevant to printed copies, where they have to cut down trees and use quite some ink, but the Kindle edition is even more expensive and is yours for a mere $84.09. Sense, it makes none. To top it off, the book unfortunately has a bit of an amateur look-‘n-feel. The cover art and the pictures used at the start of each section aren’t exactly world class. Anyway, you can’t judge a book by its cover, so we’ll take a look at the content itself.
</p>

<p style="text-align: justify">
  I have to say, this books reads like a breeze. I finished the book in a couple of days, despite being over 400 hundred pages. This is due to the writing style of the author but also because there are 31 (!) chapters in this book. That’s about 14 pages average for one chapter only. But as I said before, there are 212 figures and each chapter has its own title page (so 31 pages less to read) and usually some white space at the end. Because each chapter finished so quickly, you have the feeling you advance really fast through the book. There are a few spelling mistakes and typos here and there, but nothing too worse. The only thing that irritated me some times is the way the author emphasizes words. A hint: he emphasizes a lot. In almost every paragraph, there is something underlined, in italic or in bold or every possible combination of those three. This gives you the feeling you are reading a high-school text book or a supermarket tabloid. And <a href="http://practicaltypography.com/underlining.html">underlining is not done</a> (yes, I am painfully aware that the URL hyperlink is underlined). Any decent editor should have paid attention to that. Another point is that the author frequently repeats itself. I guess this is because he really wants to make a point (how fantastic data vault is compared with 3NF and dimensional modeling in some aspects), but after a few times I already got the point. No need in repeating it in almost every chapter. This is also the case for figures. There’s this one particular figure that I’ve encountered about 11 times throughout the book (sometimes in very slight variations). That is being thorough all right.
</p>

<p style="text-align: justify">
  OK, so there may be some improvements possible for form and layout, but what about the data vault stuff itself? Well, it is explained pretty well. If you have had no exposure to data vault yet, this book will teach you a solid understanding of its principles (I consider myself to this category of readers). I guess if you are a more advanced data vault modeler, there is some nuggets here and there to pick up or you can use the book as a reference.
</p>

<p style="text-align: justify">
  The first section (Data Vault Ensemble, which teaches you why data vault is what it is) and the third section (Working with Data Vault) really dive into data vault and how you should design such a model, but there’s a large section in the middle – Data Warehousing and Business Intelligence – which is a bunch of loosely tied chapters about a variety of subjects. Some of these chapters are in my opinion not needed to explain data vault or agile modeling. They are filler and can easily be omitted from the book. It would have made the book at least 100 pages thinner (and probably a bit cheaper in price). At the back cover Hans says “This is a guide to data vault modeling and at the same time an exploration of the broader concepts surrounding data warehousing & business intelligence today”, so I should not have been that surprised about those chapters, but on the other hand, if the book contains the words “agile data warehouse” and “data vault”, I expect a deep dive into both subjects. The problem is that because of all those <em>broader concepts</em>, these two subjects don’t get the attention they deserve. Especially the agile part. From time to time the author mentions agile and how data vault can quickly adapt to changes and how fast it can be loaded because of parallelism and how agile all of this is, but it never goes any deeper. There is no treatment on how to develop the data vault in an agile project setting. How to deal with scrum or sprints or whatever. None of that. But I can still live with it, I read the book to learn more about data vault and the book succeeded in that goal. It seems the word agile is just thrown into the title to serve as link bait.
</p>

<p style="text-align: justify">
  However, I would have liked some more detailed examples. Most of the time it is a really easy example about customers and customers classes and in some cases there is also a sale, products and employees involved, but nothing more complicated than that. This is in my opinion the biggest downside of the book. I really would have liked to see for example a whole transactional process modeled in 3NF, how it is ported into data vault and how at the end the (dimensional) data marts are generated. The book stays too basic. The last chapter of the book – titled Comparison Example – shows some tables with actual rows and data and how they are transformed. I was looking forward to that chapter while reading through the book because I hoped it would finally give a thorough hands-on example. Alas, it was the same customer table used in any other example. This book is not enough hands-on. I guess you have to follow the (paid) courses to get more hands-on material.
</p>

<p style="text-align: justify">
  The conclusion. I may have sounded a bit harsh in the previous paragraphs, but don’t get me wrong. I liked reading the book and I definitely learned a lot about data vault. I just wished the book got more hands-on examples (and maybe a decent editor). I do recommend this book to everyone who wants a solid introduction to data vault (there are not that many alternatives to be honest).
</p>