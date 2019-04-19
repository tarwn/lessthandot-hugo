---
title: 'SQL Quiz: Toughest Challenges'
author: George Mastros (gmmastros)
type: post
date: 2008-12-09T19:48:45+00:00
ID: 242
url: /index.php/itprofessionals/projectmanagement/sql-quiz-toughest-challenges/
views:
  - 4240
rp4wp_auto_linked:
  - 1
categories:
  - Project Management

---
[Chris Shaw posted a new SQL Quiz][1] where he asks: “What are the largest challenges that you have faced in your career and how did you overcome those?”

[Denny Cherry (@mrdenny on twitter)][2] tagged [SQLDenis][3] who then tagged me so here is my story:

_**The biggest problem**_ I've ever encountered was based on a rookie mistake I made several years ago. I wrote an application that would take vehicle position data and allow you to view and report on the data. Each vehicle would report its position every 10 seconds, and my customer owned approximately 100 vehicles. This amounted to roughly 10 positions per second. No big deal.

I wrote a stored procedure that would first check to see if we had duplicate data. If it was, don't store the data (it's already in the table). If it was unique (VehicleId, PositionDateTime, Latitude & Longitude), then store it in the table. After installing the database and relevant software at the client location, everything appeared to work well. That is... until several months later. The CPU usage on the client's server was pegged a 100%, and it was my fault! You see, I hadn't tested the system with millions of rows of data in the table. It was taking 20 seconds to determine if data was unique. So, each 10 seconds worth of positional data was taking 200 seconds to process. The server just couldn't keep up.
  
It took me several hours to diagnose the problem, and ½ a minute to correct it. I simply added an index on the relevant columns. Suddenly, the CPU usage dropped like a stone to 2 or 3 percent.

This occurred several years ago, and has taught me valuable lesson. Always test with a large database.

_**A second challenge**_, that probably plagues me to this day, is putting too much code in the database. Four or 5 years ago, I wrote a stored procedure that would calculate turn-by-turn driving directions based on map data stored in the database and a list of roads stored in a table. Unfortunately, I didn't know the order in which the roads were travelled, making the problem infinitely more difficult. Ultimately, I was able to write this procedure and it worked incredibly well, except it was slow. Eventually, I re-wrote this functionality using a front-end language. The result was same, but it executed in a fraction of the time. Another valuable lesson learned. 

Just because you can, doesn't mean you should! 

So now it is my turn to tag a victim: [Ted Krueger][4]

 [1]: http://chrisshaw.wordpress.com/2008/12/09/sql-quiz-part-2-2/
 [2]: http://itknowledgeexchange.techtarget.com/sql-server
 [3]: http://sqlblog.com/blogs/denis_gobo/archive/2008/12/09/10409.aspx
 [4]: /index.php/ITProfessionals/EthicsIT/an-ego-will-only-hurt-you-toughest-chall