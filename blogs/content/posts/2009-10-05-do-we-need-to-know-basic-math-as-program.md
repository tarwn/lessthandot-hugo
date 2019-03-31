---
title: Do we need to know basic math as programmers?
author: SQLDenis
type: post
date: 2009-10-05T12:11:53+00:00
url: /index.php/itprofessionals/ethicsit/do-we-need-to-know-basic-math-as-program/
views:
  - 70128
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
tags:
  - math
  - rant

---
This is kind of a semi rant so if you don&#8217;t like to read those kind of things feel free to skip this post.

How much math does a programmer need to know to do his job? These days with all the frameworks that exists you don&#8217;t need to know how to do a square root, a power function or a quick sort these will likely already be built in. What perplexes me is that someone would come out with a function to flip a sign. So if the number is 5 make it -5 and if it is -5 make it 5. Isn&#8217;t this simple math? Multiply by -1 and you are done!!

Take a look at this function that someone created on [StackOverflow][1]

> Math opposite sign function?
  
> Does such function exist? I created my own but would like to use an official one:

<pre>private function opposite(number:Number):Number
        {
                if (number &lt; 0)
                {
                        number = Math.abs(number);
                }
                else
                {
                        number = -(number);
                }
                return number;
        }</pre>

If we visit the Java forums it doesn&#8217;t get any better, take a look at this thread: [How do you Invert signed numbers?][2]

> Hi there. 
> 
> A simple question: How can I invert the sign of an integer? ie) convert -12 to +12? No doubt there is a really simple way to do this, but I&#8217;m stumped, I cannot find anything and I&#8217;ve been working on this program all day. 
> 
> I&#8217;ll be very grateful to anyone who can let me know if Java provides a way to do this, or point me in the right direction if it takes some tweaking of binary numbers using the Math package or something. 
> 
> Cheers!

Here is one &#8216;solution&#8217;

<pre>int x = numberToInvertSign;
boolean pos = x &gt; 0;
for(int i = 0; i &lt; 2*Math.abs(x); i++){
   if(pos){
      numberToInvertSign--;
   }
   else{
      numberToInvertSign++;
   }
}</pre>

You have to love this &#8216;gem&#8217; (of course this is someone messing with the original poster)

<pre>switch (i)
{
  case 1: return -1;
  case 2: return -2;
  case 3: return -3;
  // ... etc, you get the proper pattern
}</pre>

Looking at the php documentation for the [ABS function][3] we find a bunch of comments and replies to create a sign function



Isn&#8217;t this just basic math that was taught to us in grammar school? Of course it doesn&#8217;t stop here, how many people these day can convert numbers between hexadecimal (base-16), binary (base-2) and decimal (base-10) without using a tool?
  
Of course the future is that people will rely more and more on frameworks and wizards, this could be bad if you need to fix something in your code. Take a look at jQuery, people just love this because JavaScript is hard and they don&#8217;t have to deal with it&#8230;the tool shields them from that. Another example is ORM&#8230;.people hate writing queries and just want to create some classes and then some tool will spit out all the CRUD code for them. Sometimes these tools produce some bad SQL and it takes a effort to figure out why your calls to the DB are suddenly slow

Enough ranting for the day&#8230;.
  
What do you think is it inexcusable that a programmer does not know that you can flip a sign by doing the following

return -number 

or 

return number * -1

 [1]: http://stackoverflow.com/questions/1059480/math-opposite-sign-function
 [2]: http://forums.sun.com/thread.jspa?threadID=5404590&start=0
 [3]: http://us.php.net/manual/en/function.abs.php#58508