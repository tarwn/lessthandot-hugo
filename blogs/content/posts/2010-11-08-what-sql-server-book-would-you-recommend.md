---
title: What SQL Server book would you recommend for a Sybase and Oracle admin?
author: SQLDenis
type: post
date: 2010-11-08T09:09:11+00:00
ID: 946
url: /index.php/datamgmt/dbprogramming/what-sql-server-book-would-you-recommend/
views:
  - 3480
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - books
  - sql server 2008

---
Someone I know has over 10 years experience with managing Sybase and Oracle boxes. He now needs to also manage some SQL Server boxes. What books would you recommend? This is to manage 2008 instances, there are no 2000 instances anymore 🙂

Ted Krueger gave me the following recommendation

> 10 years experience on any database server should give a solid foundation so I'd venture into throwing him into the deep end with a book like Profesisonal SQL Server 2008 internals and troubleshooting 
> 
> That book is good and has a great foundation for SQL Server itself. Of course the problem is, it is 2008. We all know 2008 is great but how many are on 2005 and some 2000. So the book recommendations would still cause headaches when finding answers and trying them when you are hitting a wall with rewriting for DMV changes etc.. 
> 
> Now jumping in deep. Grab Kalen, Adam, Conor, Kimberly and Pauls book, [SQL Server Internals][1]
> 
> That one is hardcore. I still really like Rod's book, [SQL Server 2008 Administration in Action][2]. That read was a nice level for a person with a solid background. 
> 
> Grants book on query tuning is awesome. Even if this one is 2008, it is a foundation. [SQL Server 2008 Query Performance Tuning Distilled][3]
> 
> Pushing more Fritchey work, [the execution plans book][4] he did for Red Gate is my one way admin execution plan tuning reference. I never leave home without it It doesn't go through the 70+ operations but it shows the main ones and creates a foundation of how the optimizer does things along with the effect on those and tuning them. Tuning is a big DBA task to me so learning that off the bat is critical IMO 

Some other people recommended some books as well, I am thinking of these 3 books now.

[Microsoft SQL Server 2008 Administration for Oracle DBAs][5]
  
[SQL Server 2008 Administration in Action][2]
  
[DBA Survivor: Become a Rock Star DBA][6]

What do you think, leave me a comment if you can think of something else. Remember this will be for an administrator, he doesn't have to know how the ROW_NUMBER() function works

 [1]: http://www.amazon.com/gp/product/0735626243?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0735626243
 [2]: http://www.amazon.com/gp/product/193398872X?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=193398872X
 [3]: http://www.amazon.com/gp/product/1430219025?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=1430219025
 [4]: http://www.amazon.com/gp/product/1906434026?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=1906434026
 [5]: http://www.amazon.com/gp/product/0071700641?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0071700641
 [6]: http://www.amazon.com/gp/product/1430227877?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=1430227877