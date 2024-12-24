---
title: What was your first computer and what were some of your favorite games?
author: SQLDenis
type: post
date: 2009-02-17T17:39:00+00:00
ID: 329
excerpt: |
  The first computer I ever bought was a commodore 128 (I actually received it as a gift for my 16th birthday)
  
   
  This baby had 128K (not MB) of RAM, 4 sound channels and 16 colors
  With the C128 you had a C64 built in and you could run CP/M (it came w&hellip;
url: /index.php/itprofessionals/ethicsit/what-was-your-first-computer-and-what-we/
views:
  - 26824
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
tags:
  - commodore
  - gaming

---
The first computer I ever bought was a commodore 128 (I actually received it as a gift for my 16th birthday)

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Commodore128_01.png?mtime=1357605651"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Commodore128_01.png?mtime=1357605651" width="400" height="276" /></a>
</div>

This baby had 128K (not MB) of RAM, 4 sound channels and 16 colors
  
With the C128 you had a C64 built in and you could run CP/M (it came with a floppy)
  
I almost always booted up C64, this gave you 39KB free memory to use, the speed was 1MHZ, the C128 could run at 2MHZ but then the screen would go dark before you switched back to 1MHZ. The C128 came with BASIC built in, I had a taperecorder so that I could store and retrieve programs games. This was such a nuisance, if your friend gave you a game and the heads on his recorder were aligned different you could not load the game, you would have to use a screwdriver to fix the azimuth. It would take up to 30 minutes to load a game if you didn't have a turbo.

I still remember the great games from that time, here are some of my favorites

**[1942][1]</p> 

[<img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/1942_02.jpg?mtime=1357605697" width="216" height="189" />][2]
  
[yie-ar kung fu][3]
  
![][4]

[kung fu master][5]
  
![][6]

[Zaxxon][7]
  
![][8]

[Ghost N Goblins][9]
  
<img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/SQLDenis/Ghost goblins.png?mtime=1355064493" width="640" height="400" /></strong>

The best part about the games is that you could change the value in an address space after you loaded a game but before typing run
  
You would use POKE for that, examples:

POKE 43719,234 POKE 43720,234 POKE 43721,234 Invincibility
  
POKE 44731,76 POKE 44732,253 POKE 44733,174 All doors unlocked
  
POKE 34202,200 SYS 2060 Unlimited lives 

Here is a list of common pokes: http://ready64.org/articoli/\_files/043\_pokesc64.txt

Programming on the commodore was primarily done in BASIC or assembler (built in) but you could also buy a C compiler, Oxford Pascal or many other languages.

Here is an example of basic

```vb
10 PRINT "THIS IS THE MAIN PROGRAM",
20 GOSUB 1000
30 PRINT "AND AGAIN";
40 GOSUB 1000
50 PRINT "AND THAT IS ALL."
60 STOP
1000 REM SUBROUTINE STARTS HERE
1010 PRINT "THIS IS THE SUBROUTINE,"
1020 RETURN
```

Here is some assembler language

```
LDA $5000
ASL
CLC
ADC $5000
STA $5000
BRK
```

Now I will tag a bunch of people, I want to know what your first computer was and the top 3 of your favorite games

[Brent Ozar][10] [@brento][11]

[Denny Cherry][12] [@mrdenny][13]

[Michelle Ufford][14] [@sqlfool][15]

[SQLBatman][16] [@sqlbatman][17]

[Jeremiah Peschka][18] [@peschkaj][19]

[Jason Massie][20] [@statisticsio][21]

[Mladen Prajdic][22] [@MladenPrajdic][23]

For all the ones I did not tag, feel free to leave a comment

 [1]: http://en.wikipedia.org/wiki/1942_(video_game)
 [2]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/1942_02.jpg?mtime=1357605697
 [3]: http://en.wikipedia.org/wiki/Yie_Ar_Kung-Fu
 [4]: http://upload.wikimedia.org/wikipedia/en/thumb/5/59/Yiearkungfu.png/180px-Yiearkungfu.png
 [5]: http://en.wikipedia.org/wiki/Kung_Fu_(video_game)
 [6]: http://upload.wikimedia.org/wikipedia/en/thumb/6/66/Kung_fu_master_mame.png/180px-Kung_fu_master_mame.png
 [7]: http://en.wikipedia.org/wiki/Zaxxon
 [8]: http://upload.wikimedia.org/wikipedia/en/thumb/4/45/Zaxxon.png/225px-Zaxxon.png
 [9]: http://en.wikipedia.org/wiki/Ghosts_'n'_Goblins
 [10]: http://www.brentozar.com/
 [11]: http://twitter.com/brento
 [12]: http://itknowledgeexchange.techtarget.com/sql-server/
 [13]: http://twitter.com/mrdenny
 [14]: http://sqlfool.com/
 [15]: http://twitter.com/sqlfool
 [16]: http://sqlbatman.com/
 [17]: http://twitter.com/sqlbatman
 [18]: http://facility9.com/
 [19]: http://twitter.com/peschkaj
 [20]: http://statisticsio.com/
 [21]: http://twitter.com/statisticsio
 [22]: http://weblogs.sqlteam.com/mladenp/
 [23]: http://twitter.com/MladenPrajdic