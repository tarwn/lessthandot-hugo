---
title: Post 300 or why all developers should be blogging
author: SQLDenis
type: post
date: 2010-08-21T10:59:25+00:00
ID: 882
url: /index.php/itprofessionals/ethicsit/post-300-or-why-all-developers-should-be/
views:
  - 18527
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
tags:
  - blogging
  - code library

---
<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Authors.PNG" alt="" title="" width="198" height="136" />
  
So this is my 300th post on lessthandot, I decided to make this a post about why I am blogging and why you should too. There are a couple of things that are beneficial for developers who blog, these are

  * You will write better code and research more
  * You will discover new blogs and interact with your peers
  * You will have a code library online
  * Your blog will be part of your business card

Let's take a look at these 4 items listed above.

# You will write better code and research more

This one can be pretty harsh, you wrote that awesome 50 line piece of code, you created a blog post with that code only to be told by someone that why didn't you just use the built in function. Here is such an example of some code that flips the sign on a number.

```java
int x = numberToInvertSign;
boolean pos = x > 0;
for(int i = 0; i < 2*Math.abs(x); i++){
   if(pos){
      numberToInvertSign--;
   }
   else{
      numberToInvertSign++;
   }
}
```

Of course all you needed was multiply the number by -1 🙁 

I did blog about this piece of code in the [Do we need to know basic math as programmers?][1] blog post, just take a look at all those comments about math and programming there.

Something like that piece of code will happen to everyone, nobody knows all that there is to know. You can look at it two ways, either you hate the person that left the comment or you like the comment because now you have learned something new. After you get a couple of comments from people telling you what you posted is pretty much crap, you will do more research and make sure that the stuff you post is of better quality. You can easily see this effect after a couple of years when you look at the first 10 blog posts you have created and then compare those to the last 10 blog posts you have created, the stuff that is more recent will be of a higher quality.

# You will discover new blogs and interact with your peers

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/Comments.PNG" alt="" title="" width="188" height="119" />
  
Once you start blogging it might be quiet in the beginning, but after you create more and more content people will find you through search engines, twitter and other social sites. Once they find you you will get comments or track backs, of course you want to know what they think about your posts so you will check out those trackbacks and links from the comments. Doing that will help you discover interesting and helpful blog, then when you leave a comment there, the visitors from those blogs will check your blog out.

# You will have a code library online

```c
/*
  GNOT General Public License!
  (c) 1995-2007 Microsoft Corporation
*/
#include "dos.h"
#include "win95.h"
#include "win98.h"
#include "sco_unix.h"
class WindowsVista extends WindowsXP implements Nothing
{
 int totalNewFeatures = 3;
 int totalWorkingNewFeatures = 0;
 float numberOfBugs = 345889E+08;
 bool readyForRelease = FALSE;

 void main()
 {
  while (!CRASHED)
  {
   if (first_time_install)
   {
    if (installed RAM < 2GB || processorSpeed < 4GHz)
    {
     MessageBox("Hardware incompatibity error.");
     GetKeyPress ();
     BSOD();
    }
   }
   Make10GBswapfile():
   SearchAndDestroy(FIREFOX|OPENOFFICEORG|AHYTHING_GOOGLE);
   AddRandomDriver();
   MessageBox("Driver incompatibily error.");
   GetKeyPress();
   BSOD();
  }

  //printf("Welcome to Windows 2000");
  //printf("Welcome to Windows XP");
  printf("Welcome to Windows Vista");
  if (still_not_crashed)
  {
   CheckUserLicense();
   DoubleCheckUserLicense();
   TripleCheckUserLicense();
   RelayUserDetailsToRedmond();
   DisplayFancyGraphics();
   FlickerLED(hard_drive);
   RunWindowsXP();
   return LotsMoreMoney;
  }
 }
}
```

I cannot tell you how many times I search my old blog posts for either myself or co-workers, the post [How to enable xp_cmdshell and Ad Hoc Distributed Queries on SQL Server 2005][2] is probably my most searched post. Whenever I or someone else installs SQL Server, the first issue is always openrowset and xp_cmdshell don't work..what do I do? Of course I have all the source code in SVN but when a friend or a newsgroup user asks how to do something, it is much easier to just give them the link to the blog post. Sometimes a person will leave a comment with another way to accomplish the same task, this is great because now you have both of them in the same place.

# Your blog will be part of your business card

[<img src="http://farm1.static.flickr.com/160/344673538_d4961faa1c_m.jpg" width="240" height="224" alt="Business Card Cube Gift Box (mangled)" />][3]
  
Whenever I interview people for a position I ask them if they have a blog so that I can check it out. The code that you have on your blog will give prospective employers some sense of your coding capabilities. Yes, you can have that fancy schmancy title on your business card but it does not really tell me anything. I worked at a company with 5 employees once, one was CEO, the other CTO, the third an Art Director and the other two programmers, in reality the _CTO_ was more like a mid level programmer, it is just that he knew where all the servers were and what the passwords were. 

Usually if someone has a blog it means that he enjoys programming and is not a person who hates coding. He/she might hate the framework/language he is stuck with at work, but he might be able to convince management to use something better that he discovered in his own time while blogging about it. A person who has a blog is also not the 9 to 5 coder. who when getting home never looks at code and doesn't do any research on the weekends.

# What to blog about and how to keep the ideas coming

[<img src="http://farm1.static.flickr.com/134/318947873_12028f1b66_m.jpg" width="240" height="186" alt="Questions" />][4]
  
Technology is changing at an incredible pace so there is no lack of new material to write about. Once that new and shiny beta or CTP version of some of your favorite software comes out, download it, install it and start playing with it. Once you played around with it, start creating blog posts about the new version, you can list what you like, what you don't like, what the new features are and which feature the most beneficial will be to you and others.

One of the best ways for me to get new ideas is participating in newsgroups, I think that at least half of my blog posts are the result of answering questions in newsgroups and forums. Once you are active for a bit on the newsgroups/forums you will see that there are a whole lot of questions that are variations on a handful of questions, this was the reason that I wrote the [The Ten Most Asked SQL Server Questions And Their Answers][5] post. Now I can just give the person the link and be done with it, this saves me time and the person will have something he can bookmark for future references. In this case 120 people bookmarked the [The Ten Most Asked SQL Server Questions And Their Answers][5] post on delicious: http://www.delicious.com/url/e73c8b3f59393bf8222aaff053bd21b6

Another thing you can do is write about that awesome programming book you read and perhaps even do an interview with the author about the book. Here are some examples:
  
I reviewed the book here: [Review of Pro SQL Server 2008 Relational Database Design and Implementation][6] and interviewed the author here: [Interview With Louis Davidson Author of Pro SQL Server 2008 Relational Database Design and Implementation][7]
  
I reviewed the book here: [Review of SQL Server 2008 Administration in Action][8] and interviewed the author here: [Interview With Rod Colledge About The Book SQL Server 2008 Administration in Action][9]

# Conclusion

Blogging is an excellent way to get your name out there and to show the world some of the code that you write. It also will help you become a programmer who takes less time to write code at work, this is especially true if you are already blogging about the current version of a product but you are just about to start using that product at work. The best part about blogging is that it doesn't cost you anything, go to [blogger][10], [wordpress][11] or any of the other blogging sites, pick a name, a template and you are ready to go

**Finally I want to ask you, are you blogging, and if you are is it helping you becoming a better developer?**

 [1]: /index.php/ITProfessionals/EthicsIT/do-we-need-to-know-basic-math-as-program
 [2]: /index.php/DataMgmt/DataDesign/how-to-enable-xp_cmdshell-on-sql-server-2005
 [3]: http://www.flickr.com/photos/randycox/344673538/ "Business Card Cube Gift Box (mangled) by Randy Cox, on Flickr"
 [4]: http://www.flickr.com/photos/oberazzi/318947873/ "Questions by Oberazzi, on Flickr"
 [5]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1
 [6]: /index.php/WebDev/WebDesignGraphicsStyling/review-of-pro-sql-server-2008-relational
 [7]: /index.php/DataMgmt/DataDesign/interview-with-louis-davidson-author-of-
 [8]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/review-of-sql-server-2008-administration
 [9]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/interview-with-rod-colledge-about-the-bo
 [10]: http://www.blogger.com/home
 [11]: http://wordpress.com/