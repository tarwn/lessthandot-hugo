---
title: Five Google Wave Invitations are up for grabs
author: SQLDenis
type: post
date: 2009-10-01T09:41:09+00:00
ID: 573
url: /index.php/itprofessionals/ethicsit/five-google-wave-invitations-are-up-for/
views:
  - 21516
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
tags:
  - google wave

---
I have 5 Google Wave Invitations that I will give away today

![Google wave][1]

All you need to do is leave a comment with your twitter user name telling me why you think you need a Google Wave invite. I will pick 5 people randomly and today at 5PM EST I will DM the winners

Keep in mind that the invites won't be activated right away, according to the wave team

> Invite others to Google Wave
> 
> Google Wave is more fun when you have others to wave with, so please nominate people you would like to add. Keep in mind that this is a preview so it could be a bit rocky at times.
> 
> Invitations will not be sent immediately. We have a lot of stamps to lick.
> 
> Happy waving!

Good Luck!!!

Here is the SQL Code that will determine who will win

First the create table and insert statement

```sql
CREATE TABLE #TEMP(LuckyWinner varchar(500))


 
insert #TEMP Values('http://twitter.com/misterjp')
insert #TEMP Values('http://sqlchicken.com')
insert #TEMP Values('@Jorriss')
insert #TEMP Values('http://www.markstahler.ca')
insert #TEMP Values('http://cedmax.net ')
insert #TEMP Values('http://twitter.com/macbaed ')
insert #TEMP Values('@ovoro')
insert #TEMP Values('@kooshmoose')
insert #TEMP Values('FUJINAKA Tohru ')
insert #TEMP Values('dharmubaba')
insert #TEMP Values('tuteken')
insert #TEMP Values('@AaronCorbin ')
insert #TEMP Values('somercamb')
insert #TEMP Values('@russconklin')
insert #TEMP Values('Rohit ')
insert #TEMP Values('@colombiancoffee')
insert #TEMP Values('feluli')
insert #TEMP Values('tsvete')
insert #TEMP Values('traingamer')
insert #TEMP Values('Naomi')
insert #TEMP Values('Amy')
insert #TEMP Values('@alejandro_amo')
insert #TEMP Values('sqlsister ')
insert #TEMP Values('indyan')
insert #TEMP Values('Conrado')
insert #TEMP Values('spocky12')
insert #TEMP Values('Victor Anderson')
insert #TEMP Values('trocolo ')
insert #TEMP Values('Nick Piccone')
insert #TEMP Values('@veetahl')
insert #TEMP Values('fulch92a')
insert #TEMP Values('jasonrmerrill')
insert #TEMP Values('sqlsamson')
insert #TEMP Values('Todd')
insert #TEMP Values('@AaronBertrand')
insert #TEMP Values('@aubergineshriek')
insert #TEMP Values('@bpaisley69')
insert #TEMP Values('@andryou')
insert #TEMP Values('wookielnx')

```
The query below will pick the 5 random winners

```sql
SELECT TOP 5 * 
FROM #TEMP
ORDER BY NEWID()
```

**And the lucky winners are: @AaronBertrand, @misterjp, @Jorriss,@russconklin and sqlsister. Invitations have been entered into Google Wave...now you have to wait for the Wave team**

 [1]: http://imgur.com/1mKn7.png