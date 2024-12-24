---
title: Master Data Services (Second Edition) – Review
author: Koen Verbeeck
type: post
date: 2014-01-31T13:45:14+00:00
ID: 2329
url: /index.php/datamgmt/master-data-services-second-edition-review/
views:
  - 12545
rp4wp_auto_linked:
  - 1
categories:
  - Book Review
  - Data Management
  - Master Data Services (MDS)
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - master data management
  - master data services
  - mds
  - sql server 2012
  - syndicated

---
<p style="text-align: justify">
  For the preparation of my upcoming talk about SQL Server 2012 Master Data Services on the <a href="http://www.element61.be/e/newsevt-detail.asp?ResourceId=721">Microsoft Business Analytics Day</a> (hosted by my company <a href="http://www.element61.be/">element61</a>), I read the book <a href="http://amzn.to/1Oeichj">Master Data Services 2<sup>nd</sup> edition</a> by Tyler Graham.
</p>

<p style="text-align: justify">
  I had almost no prior experience with MDS up to this point (I very quickly glanced over it in preparation for the 70-463 exam and I guess I didn't answer a lot of questions about MDS correctly), so I needed a book that could introduce me to the concepts of MDS and how I can effectively manage master data using this tool.
</p>

<p style="text-align: justify">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/MDS_book2_banner.png"><img class="alignleft  wp-image-2331" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/MDS_book2_banner.png" alt="MDS_book2_banner" width="207" height="228" /></a>
</p>

<p style="text-align: justify">
  Tyler Graham was part of the development team of SQL Server Master Data Services right from the start, so if anyone is qualified about writing a book about MDS it's probably him. You can notice his profound knowledge throughout the book, as he frequently delves deeper in the MDS web service (I didn't even know there was one) and references hidden stored procedures and views to make MDS do your bidding. The book accomplishes its goal: I know have a good working knowledge of the tool and if I have to use it in a project tomorrow, I know where to start.
</p>

<p style="text-align: justify">
  The book starts with an introduction to manager master data in general, which is very welcome for someone new to the field, just like me. The book then thoroughly explains how to install and configure MDS. In the next chapter, the business use case used throughout the book is introduces and in the following chapter you learn about creating models.
</p>

<p style="text-align: justify">
  Chapter 5 explains how you can import data into MDS, using the various staging tables. I would have put this chapter later in the book, because the examples show you how to populate objects you have never even heard of and you don't exactly know what they represent or how you can use them. A bit confusing. The author talks how you can automate the import process using stored procedures – very useful when you plan on using SSIS to load MDS – and he says he will explain them in the next section. However, the next section is about web services and he never mentions the stored procedures again. A missed opportunity...
</p>

<p style="text-align: justify">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/WhereAreTheStoredProcedures.png"><img class="alignleft size-medium wp-image-2332" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/WhereAreTheStoredProcedures-300x237.png" alt="WhereAreTheStoredProcedures" width="300" height="237" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/WhereAreTheStoredProcedures-300x237.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/WhereAreTheStoredProcedures.png 724w" sizes="(max-width: 300px) 100vw, 300px" /></a>
</p>

<p style="text-align: justify">
  The next chapters explain how to create hierarchies, collections, business rules and so on. The whole time the same business case is used for the examples: a clothing company managing financial and product data with MDS. The examples are real-life and they help you to understand how to work with the product.
</p>

<p style="text-align: justify">
  Then there's a whole chapter on using Excel as the front-end for MDS. This is great, as this really opens up MDS for the business user, which gives MDS an advantage over its competitors. However, the book uses a sample workbook you can supposedly download from <a href="http://www.mdsuser.com/">mdsuser.com</a>. But the Excel is nowhere to be found in this site. The book also mentions another site you can download samples from, but that site doesn't even exist anymore.
</p>

<p style="text-align: justify">
  At that point we're at chapter 11, which is a long chapter on how to implement security. The author goes into great detail to explain how it works in MDS. This chapter is certainly useful as a reference when you are implementing permissions in MDS. Chapter 12 shortly explains how to create subscription views so that you can extract data from the system (which is basically the point of a master data implementation, otherwise it wouldn't be a much use). The next chapter is all about web services, so I gladly skipped that one. The last chapter talks about advanced modelling, but is just a few pages with a few tips and a few reminders of what not to do with MDS. Very interesting though was the authors take on MDS and slowly changing dimensions.
</p>

<p style="text-align: justify">
  All in all this was a very decent book that showed me how to work with Master Data Services and how to get the most out of it. The author always explains the differences between the previous version and also warns you about what will not work (well) in MDS and how you can possibly circumvent it by fiddling around in the database.
</p>

<p style="text-align: justify">
  The biggest downside – for me personally – is something that is probably out of control of the author: the format of the eBook. The book was purchased on ebooks.com in PDF format, but the PDF look like some poorly converted eBook format (epub or mobi or something like that). There were no page numbers, no headings or footers and every page just went to the next one, leading to awkward section endings. There were also no margins, which makes it difficult to decently print a page. The book is supposed to be 416 pages long, but the PDF has only 337 pages. Quite a difference. Maybe ebooks.com throw the section about stored procedures away with their lousy conversion. Bad ebooks.com.
</p>

<p style="text-align: justify">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/WorstFormatEver.png"><img class="alignleft size-medium wp-image-2333" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/WorstFormatEver-300x124.png" alt="WorstFormatEver" width="300" height="124" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/WorstFormatEver-300x124.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/WorstFormatEver.png 755w" sizes="(max-width: 300px) 100vw, 300px" /></a>
</p>

<p style="text-align: justify">
  This might be normal for an actual eBook for reading on a Kindle or a tablet (I have no idea, I own neither of both), but when I buy a PDF I expect it to look like the actual book. Again, this has nothing to do with the efforts of the author: he wrote an excellent book on Master Data Services. The samples are OK with this book, but a few more effort could be put into them. All in all I recommend this book to everyone who wants to learn more about SQL Server 2012 Master Data Services. Just don't buy it from ebooks.com.
</p>