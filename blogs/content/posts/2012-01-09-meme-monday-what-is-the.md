---
title: 'Meme Monday: what is the first blogpost you wrote and when did you write it?'
author: SQLDenis
type: post
date: 2012-01-09T09:25:00+00:00
ID: 1479
excerpt: |
  What was the first blogpost you wrote? When did you write it and what was it about.
  
  Here is mine, it is from September 15, 2005, it was about something that trips people up
  
  @@IDENTITY returns wrong identity field
  When using @@IDENTITY I get the w&hellip;
url: /index.php/itprofessionals/ethicsit/meme-monday-what-is-the/
views:
  - 8473
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
  - Professional Development

---
What was the first blogpost you wrote? When did you write it and what was it about.

Here is mine, it is from September 15, 2005, it was about something that trips people up

> @@IDENTITY returns wrong identity field
  
> When using @@IDENTITY I get the wrong results
  
> I have seen this problem posted many times on newsgroups and It actually happened to a co-worker not too long ago
  
> This 'problem' can occur when you do an insert into Table A and there is at trigger defined that fires when the insert happens and this trigger does an insert into table B.
  
> What @@IDENTITY returns is actually the identity from Table B
  
> In order to get the identity back from table A you should use SCOPE_IDENTITY() instead

Here is the link to the post: [@@IDENTITY returns wrong identity field][1]

Since the first post I have blogged almost 1100 times, 678 times on the [SQL Server Code,Tips And Tricks, Perfomance Tuning][2] blog and over 400 times here on lessthandot

Let's hear your story, I am tagging the following people:

Ted Krueger [@onpnt][3]
  
Christiaan Baes [@chrissie1][4]
  
Aaron Bertrand [@AaronBertrand][5]
  
Denny Cherry [@mrdenny][6]
  
Buck Woody [@buckwoody][7]
  
Thomas LaRock [@sqlrockstar][8]
  
Kevin Kline [@kekline][9]
  
Eli Weinstock-Herman [@tarwn][10]
  
Mladen Prajdić [@MladenPrajdic][11]
  
Karen Lopez [@datachick][12]
  
Romeliz Valenciano [@bonskijr][13] 

To all the others, leave me a comment that says what the post is about, has the date and also a link to the post

 [1]: http://sqlservercode.blogspot.com/2005/09/identity-returns-wrong-identity-field.html
 [2]: http://sqlservercode.blogspot.com/
 [3]: https://twitter.com/#!/onpnt
 [4]: https://twitter.com/#!/chrissie1
 [5]: https://twitter.com/#!/AaronBertrand
 [6]: https://twitter.com/#!/mrdenny
 [7]: https://twitter.com/#!/buckwoody
 [8]: https://twitter.com/#!/sqlrockstar
 [9]: https://twitter.com/#!/kekline
 [10]: https://twitter.com/#!/Tarwn
 [11]: https://twitter.com/#!/MladenPrajdic
 [12]: https://twitter.com/#!/datachick
 [13]: https://twitter.com/#!/bonskijr