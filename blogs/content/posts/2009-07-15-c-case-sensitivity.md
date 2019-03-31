---
title: 'C# case sensitivity'
author: chopstik
type: post
date: 2009-07-15T22:46:26+00:00
url: /index.php/desktopdev/mstech/csharp/c-case-sensitivity/
views:
  - 9983
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'

---
I spent the better part of two weeks trying to troubleshoot an issue that was causing some problems. Essentially, information within the system was not being properly routed based on specific criteria. When I received the case, the issue had apparently been fixed for the customer in question so, when I attempted to recreate the problem on my local box, I was unable to reproduce it. Instead, I tried stepping through the code (which is pretty complex C# code) and then looking at the data and relevant stored procedures in the back-end SQL database. I had found a discrepancy in part of the data that had been stored, but it turned out to be ancillary to the originating cause (to be shown shortly).

Finally, someone else noted that running an update on a customer (without actually updating any information) fixed the problem. But stepping through the update functionality did not bring me any closer to finding the solution. One thing that was noticed was that it seemed to happen with customers who had alpha characters in their ID. But not all of them, only some, namely older customers. It also seemed to be related to those with lower case characters in the ID. But our database is set up to be case-insensitive. And, as it turned out, the test samples I was working with all used upper case alpha characters. When finally testing against lower case samples, the problem appeared!

But I was confused. The database is case-insensitive, so what difference does it make?! For a VB programmer (or at least one who started out with VB structure and only in the last year has moved over to C#), no difference at all. For C#, however, a great deal. I already knew that C# was sensitive to case in terms of variable names, functions, classes, etc. But it never dawned on me that strings, when compared to each other, were also case-sensitive! 🙄

They say the best lessons learned are those learned the hard way. A simple problem in the end, but one that turned out to be much harder because of my own misconceptions and assumptions. On the flip side, though, it is one lesson that will never be forgotten!