---
title: 'Book Review: Inside the SQL Server Query Optimizer'
author: Jes Borland
type: post
date: 2013-12-23T20:36:00+00:00
ID: 2214
excerpt: Want to take the "black box" mystique out of the query optimizer?
url: /index.php/itprofessionals/book-review/book-review-inside-the-sql/
views:
  - 8298
rp4wp_auto_linked:
  - 1
categories:
  - Book Review

---
<div class="bText">
  <div class="bText">
    <p>
      <img style="float: left;" src="/wp-content/uploads/users/grrlgeek/1175-QueryOptimizerCover_200h.jpg?mtime=1387829859" alt="" />I've had Benjamin Nevarez's <a href="https://www.simple-talk.com/books/sql-books/inside-the-sql-server-query-optimizer/">Inside the SQL Server Query Optimizer</a> sitting on my bookshelf for a couple of years. I recently pulled it off the shelf to read it cover-to-cover, to see what new things I could learn.
    </p>
    
    <p>
      This book is supposed to take the "magic black box" feel out of the query optimizer. Chapters range from an introduction to optimization, to index selection, to hints.
    </p>
    
    <p>
      I remember my first introduction to the parts of the query optimizer – I was at SQL Saturday Chicago and <a href="http://scarydba.com">Grant Fritchey</a> was giving a presentation to a packed room. He mentioned terms like "parsing" and "binding". I thought it was interesting, but most of my work with queries was more practical – how do I make them suck less? (Fortunately, in the past four years, I've gotten a lot better at making queries run better.) Now, I'm interested in more details around this process.
    </p>
    
    <p>
      The book starts with a discussion of what a query optimizer is, and what it does. For those of us without computer science degrees, it's a great introduction to the history of optimization, core fundamentals, and challenges. It sets a good foundation for the information that is covered in the rest of the book.
    </p>
    
    <p>
      What's the difference between a scan and a seek? What is a lookup? What are the differences between the nested loops, hash, and merge joins? How can you give SQL Server the information it needs to make the most efficient decision on which to use? The second chapter covers some of the most-frequently seen operators in execution plans.
    </p>
    
    <p style="text-align: center;">
      <img src="/wp-content/uploads/users/grrlgeek/index scan.JPG?mtime=1387830757" alt="" /><img src="/wp-content/uploads/users/grrlgeek/index seek.JPG?mtime=1387830757" alt="" /><img src="/wp-content/uploads/users/grrlgeek/Key lookup.JPG?mtime=1387830757" alt="" />
    </p>
    
    <p>
      How does the optimizer make its decisions? It's based on query cost estimates. How does it get information about costs? It looks at the statistics on indexes. The next chapter covers what statistics are, how they are calculated, and how to keep them up to date. I have a few sections highlighted here for future reference.
    </p>
    
    <p>
      Then, he dives into indexes – how your queries are written and how your indexes are created can make or break a determination by the optimizer. Knowing how it will search for indexes to use will help you create better indexes. Two tools for discovering better indexes – the DTA and DMVs are covered. (Secret? I don't like the DTA. I'd rather you know how to use the index DMVs.)
    </p>
    
    <p>
      In chapter five, Ben starts digging into the inside of the optimizer. I learned some new things about the sys.dm_exec_query_optimizer_info and sys.dm_exec_query_transformation_stats DMVs. Transformation rules are covered, which reminded me of some high school math classes. To me, it's interesting to know how the optimizer handles this, but not something I would touch very often.
    </p>
    
    <p>
      Different phases of optimization are covered as well. It's good to know the difference between trivial and full – it's helped me understand why SQL Server picked a specific plan time sometimes. It's also good to understand the phases of full.
    </p>
    
    <p>
      The next couple of chapters cover a hodge-podge of topics. How are updates (which can be an insert, update, or delete) different from a select? How does SQL Server prevent rows from being updated multiple times? What is parameter sniffing, and how can you work with it? What are query, join, and table hints, and what do you need to know before using them?
    </p>
    
    <p>
      If you are used to reading execution plans and are looking for a bit more understanding of how things work, this is a good book to pick up. Keep in mind that as things change with SQL Server 2014 coming out, you might not see some of the same behaviors on newer versions, but the information is solid nonetheless.
    </p>
    
    <p>
      Thank you, Ben, for distilling what could be a complicated technical discussion into easily-understandable pieces!
    </p>
  </div>
</div>