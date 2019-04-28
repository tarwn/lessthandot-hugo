---
title: Interview/Exam questions part 2
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-12T07:42:20+00:00
ID: 56
url: /index.php/desktopdev/mstech/interview-exam-questions-part-2/
views:
  - 2093
categories:
  - 'C#'
  - Microsoft Technologies

---
[Read my previous post.][1]

So let&#8217;s continue with question 4. This one builds on question 3. Make a class that inherits from Time and call it SportsTime. SportsTime should have a constructor like this SportsTime(int h, int m, int s, int hs). SportsTime has an added property called hundredthsofasecond. SportsTime also has a method called ToUniversalTime with hundredthsofasecond added to what we had before. SportsTime also has another method called TimeDifference which accepts a parameter SportsTime and calculates the difference between the parameter and the class you are in.

This is my solution.

```
public class SportsTime:Time
{
    private int _hundredthsofasecond;

    public SportsTime(int u, int m,int s,int hs):base(u,m,s)
    {
        if(hs&lt;0 || hs&gt;99)
        {
            _hundredthsofasecond= 0;
        }
        else
        {
            _hundredthsofasecond= hs;
        }
    }

    public int Hundredthsofasecond
    {
        get
        {
            return _hundredthsofasecond;
        }
    }

    public new string ToUniversalString()
    {
        return base.ToUniversalString() + ":" + _hundredthsofasecond.ToString();
    }

    public SportsTime TimeDifference(SportsTime t)
    {
        SportsTime temp = new SportsTime(Math.Abs(t.Hours - this.Hours), Math.Abs(t.Minutes - this.Minutes), Math.Abs(t.Secondes - this.Secondes), Math.Abs(t.Hundredthsofasecond- this.Hundredthsofasecond));
        return temp;     
    }
}```
Tested with this.

```
SportsTime sportTijd1 = new SportsTime(23, 12, 12,-1);
            Console.WriteLine(sportTijd1.ToUniversalString());
            SportsTime sportTijd2 = new SportsTime(23, 12, 12, 100);
            Console.WriteLine(sportTijd2.ToUniversalString());
            SportsTime sportTijd3 = new SportsTime(23, 12, 12, 12);
            Console.WriteLine(sportTijd3.ToUniversalString());
            SportsTime sportTijd4 = new SportsTime(23, 12, 12, 15);
            Console.WriteLine(sportTijd4.ToUniversalString());
            Console.WriteLine(sportTijd3.TimeDifference(sportTijd4).ToUniversalString());
            Console.WriteLine(sportTijd4.TimeDifference(sportTijd3).ToUniversalString());
            Time sportTijd5 = new SportsTime(23, 12, 12, 15);
            Console.WriteLine(sportTijd5.ToUniversalString());
            Console.ReadLine();```
So two very simple questions it seems, some very nice things to see if the person answering the question has grasped OO principales. 

Look especially for that at the ToUniversalString method. The next two questions elaborated on the previous two and where purely theorectic. More about this in my next post.

 [1]: /index.php/DesktopDev/MSTech/interview-exam-questions