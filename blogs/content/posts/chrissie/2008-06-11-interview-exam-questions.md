---
title: Interview/exam questions
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-11T11:39:28+00:00
ID: 54
url: /index.php/desktopdev/mstech/interview-exam-questions/
views:
  - 1664
categories:
  - Microsoft Technologies

---
Working for the government has it&#8217;s pros and cons. The con being that if you want to have a promotion then you have to do an test. So this morning I had the first of two tests for this promotion, meaning that this was the basic test on 20 out of 200 points to be won. This was for analysts and programmers alike. The second part will be more discriminatory. So lets not bore you with question number 1 and 2 (who writes use cases anyway). BTW it seemed to be a hard test since half of the room (20 people) left after reading the questions ;-))

Lets start with question number 3. I&#8217;ll try to make it short.

Make a class Time that accepts Hours, Minutes and seconds. make a constructor that looks like this Time(int h, int m, int s). The hours (h) can not be less then 0 and greater than 23 if they are then set it to 0. the same for minutes and seconds but then no greater than 59. Make three properties that only have a get method. Make a method named ToUniversalTime that returns a string. One wonders why so many people left.

So did you notice no mention of which programming language to choose. They use the word properties (not very java friendly) they already provide the signature for the constructor.

Mind you question 4 elaborates on question 3 and 5 and 6 on both of the previous.

So here is my solution in C# (because VB.Net is getting so boring).

```csharp
public class Time
{
    private int _hours;
    private int _minutes;
    private int _seconds;

    public Time(int u, int m, int s)
    {
        if (u &lt; 0 || u &gt; 23)
        {
            _hours = 0;
        }
        else
        {
            _hours = u;
        }
        if (m &lt; 0 || m &gt; 59)
        {
            _minutes = 0;
        }
        else
        {
            _minutes = m;
        }
        if (s &lt; 0 || s &gt; 59)
        {
            _seconds = 0;
        }
        else
        {
            _seconds = s;
        }
    }

    public int Hours
    {
        get
        {
            return _hours;
        }
    }

    public int Minutes
    {
        get
        {
            return _minutes;
        }
    }

    public int Secondes
    {
        get
        {
            return _seconds;
        }
    }

    public string ToUniversalString()
    {
        return _hours.ToString() + ":" + _minutes.ToString() + ":" + _seconds.ToString();
    }
}```
Did I mention that computers were off limits? And that I had to use pen and paper? 

Do you think I passed this question?

I tried it with this. And it did what it was supposed to do.

```
Time tijd1 = new Time(23,12,12);
Console.WriteLine(tijd1.ToUniversalString());
Time tijd2 = new Time(24, 12, 12);
Console.WriteLine(tijd2.ToUniversalString());
Time tijd3 = new Time(23, 60, 12);
Console.WriteLine(tijd3.ToUniversalString());
Time tijd4 = new Time(23, 12, 60);
Console.WriteLine(tijd4.ToUniversalString());
Time tijd5 = new Time(-1, 12, 12);
Console.WriteLine(tijd5.ToUniversalString());
Time tijd6 = new Time(23, -1, 12);
Console.WriteLine(tijd6.ToUniversalString());
Time tijd7 = new Time(23, 12, -1);```
Tomorrow I will discuss Question number 4

Which was nice to test basic skills.