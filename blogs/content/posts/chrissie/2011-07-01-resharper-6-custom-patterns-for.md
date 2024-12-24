---
title: Resharper 6 Custom patterns for Nunit
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-01T12:23:00+00:00
ID: 1239
excerpt: |
  Custom patterns with Resharper are cool. And can help you and your team to make your code more uniform. I know me and my team are using them ;-). 
  
  The human brain works in such a way that it will filter out any information it knows or thinks it knows&hellip;
url: /index.php/desktopdev/mstech/resharper-6-custom-patterns-for/
views:
  - 3434
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - custom patterns
  - resharper

---
Custom patterns with Resharper are cool. And can help you and your team to make your code more uniform. I know me and my team are using them ;-). 

The human brain works in such a way that it will filter out any information it knows or thinks it knows. You know that text without the vowls that you can read without a problem because your brain just knows what should be there. Ths s txt wtht vwls. Pretty sure you can read what I meant there. So the more you make something uniform the faster your brain will be able to skim over a certain text.

Now in NUnit these statements 

```vbnet
Assert.AreEqual(True, "string".Contains("s"))
Assert.AreEqual(false, "string".Contains("k"))
Assert.AreEqual("", "")```
are the same as these statements

```vbnet
Assert.IsTrue("string".Contains("s"))
Assert.IsFalse("string".Contains("k"))
Assert.IsEmpty("")```
I prefer the bottom one because less code is less bugs (maybe).

So I set about to create custom patterns in Resharper 6.

I made 3.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP1.png?mtime=1309529928"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP1.png?mtime=1309529928" width="820" height="600" /></a>
</div>

One for The IsTrue thing.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP2.png?mtime=1309529938"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP2.png?mtime=1309529938" width="820" height="600" /></a>
</div>

One for the IsFalse thing

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP3.png?mtime=1309529948"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP3.png?mtime=1309529948" width="800" height="600" /></a>
</div>

One for the IsEmpty String.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP4.png?mtime=1309529958"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP4.png?mtime=1309529958" width="800" height="600" /></a>
</div>

I will now recieve a suggestion saying I should better use IsFalse, IsTrue or IsEmpty where appropriate. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP5.png?mtime=1309529967"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP5.png?mtime=1309529967" width="457" height="90" /></a>
</div>

And I can do the Alt+Enter thing.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP6.png?mtime=1309529975"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperCP6.png?mtime=1309529975" width="527" height="93" /></a>
</div>

Rules are important in a team, a way to make it easier to enforce them is helpful.

That does not mean that rules shoud be made into a religion.