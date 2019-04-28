---
title: The difference between Invoke and BeginInvoke
author: Christiaan Baes (chrissie1)
type: post
date: 2008-10-23T11:04:30+00:00
ID: 181
url: /index.php/desktopdev/mstech/the-difference-between-invoke-and-begini/
views:
  - 6123
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET

---
As a developer you have the responsibility to stay informed and to continuously learn. Sometimes you can learn from another person&#8217;s questions and, hopefully, a good answer. Perhaps somebody has had a problem you have never faced. But perhaps you will face that problem in the future and reading about it in the past might get you to find the answer more easily (did that make sense ;-)). 

So for this reason alone, I read forums all over the place including the one on LessThanDot but more recently the one on StackOverflow too. 

And today I spotted this question.

[Whats the difference between Invoke() and BeginInvoke()][1]

And there came a very good answer to this question.

In short.

  * Delegate.Invoke: Executes synchronously, on the same thread.
  * Delegate.BeginInvoke: Executes asynchronously, on a threadpool thread.
  * Control.Invoke: Executes on the UI thread, but calling thread waits for completion before continuing.
  * Control.BeginInvoke: Executes on the UI thread, and calling thread doesn&#8217;t wait for completion.

Makes sense, doesn&#8217;t it?

 [1]: http://stackoverflow.com/questions/229554/whats-the-difference-between-invoke-and-begininvoke