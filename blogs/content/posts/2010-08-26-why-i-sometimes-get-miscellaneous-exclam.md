---
title: Why I sometimes get miscellaneous exclamation marks (!) in SQL generated emails
author: kaht
type: post
date: 2010-08-26T17:28:06+00:00
excerpt: |
  Using SQL server to generate automatic emails, I've noticed from time to time that the emails will miscellaneously contain exclamations throughout the mail.
  
  Here's an example to replicate the situation.  We'll generate a table with multiple rows of t&hellip;
url: /index.php/webdev/serverprogramming/why-i-sometimes-get-miscellaneous-exclam/
views:
  - 10193
rp4wp_auto_linked:
  - 1
categories:
  - Server Programming

---
Using SQL server to generate automatic emails, I&#8217;ve noticed from time to time that the emails will miscellaneously contain exclamations throughout the mail.

Here&#8217;s an example to replicate the situation. We&#8217;ll generate a table with multiple rows of the alphabet and email the contents:

<pre>declare @t table (letters varchar(100))
declare @counter int
declare @string varchar(8000)
set @counter = 0
set @string = ''

insert into @t
select 'abcdefghijklmnopqrstuvwxyz'

while (@counter < 128) begin
   insert into @t
   select letters from @t
   set @counter = @@rowcount
end

select @string = @string + letters + '<br&gt;'
from @t

select @string

declare @mailObj int
declare @hr int

exec @hr = sp_OACreate 'CDO.Message', @mailObj out
exec @hr = sp_OASetProperty @mailObj, 'From', 'sender@server.com'
exec @hr = sp_OASetProperty @mailObj, 'HTMLBody', @string
exec @hr = sp_OASetProperty @mailObj, 'Subject', 'test'
exec @hr = sp_OASetProperty @mailObj, 'To', 'recipient@server.com'
exec @hr = sp_OAMethod @mailObj, 'Send', NULL
exec @hr = sp_OADestroy @mailObj</pre>

This should kick out the alphabet repeated 256 times, with a <br> tag after each alphabet. By examining the result in query analyzer, it should look just fine. However, when you go check the email that got sent, you&#8217;ll find parts of the text that look like this:

<code class="codespan">abcdefghijklmnopqrstuvwxyz<br />
abcdefghijklmnopqrstuvwxyzabcdef! ghijklmnopqrstuvwxyz<br />
abcdefghijklmnopqrstuvwxyz<br />
.<br />
.<br />
.<br />
abcdefghijklmnopqrstuvwxyz<br />
abcd! efghijklm! nopqrstuvwxyz<br />
abcdefghijklmnopqrstuvwxyz</code>

Fortunately, the solution is fairly simple. Microsoft Outlook seems to insert these exclamation marks randomly throughout the email when the mail contains no line feeds.

So, stick a line feed after each alphabet and the exclamation marks will disappear from the email:

<pre>select @string = @string + letters + '<br&gt;' + char(10)
from @t</pre>

Piece of cake B)