---
title: SQL Server Rounding Methods
author: George Mastros (gmmastros)
type: post
date: 2008-12-11T21:17:13+00:00
ID: 248
excerpt: "There are various ways to round a number, and most of us don't give it much thought, but we should.  There are several methods for rounding: Round Up, Round Down, Round Away From Zero, Round Toward Zero, and Round Toward Even (also known as bankers roun&hellip;"
url: /index.php/datamgmt/datadesign/sql-server-rounding-methods/
views:
  - 85121
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
There are various ways to round a number, and most of us don&#8217;t give it much thought, but we should. There are several methods for rounding: Round Up, Round Down, Round Away From Zero, Round Toward Zero, and Round Toward Even (also known as bankers rounding, unbiased rounding, Gaussian rounding, and statisticians rounding), and Stochastic Rounding.

It&#8217;s important to note that in most cases, the Round function produces the result we want. The intended purpose of the code shown below is to affect only those cases where a number can be rounded up or down because the value rounded is exactly mid-way between the rounded up number and the rounded down number. For example, when you round 3.5 to the nearest whole number, what should the result be? 3 or 4? When you perform many calculations on rounded numbers, a Stochastic round or a bankers round will be more accurate than any other method because (statistically), the edge cases will cancel each other out, resulting in a more accurate result.

It&#8217;s interesting to note that many client side languages like vb6 and the .net framework implement bankers rounding. Starting with .net framework 2.0, there is an optional 3rd argument to the round function that allows you to modify the behavior. In any event, this differs from SQL Server&#8217;s implementation of rounding, which is always &#8216;Round away from zero&#8217;. For example, round(4.25, 1) = 4.3 and Round(-4.25, 1) = -4.3. Both numbers are rounded away from zero. In VB, Round(4.25, 1) = 4.2

Let&#8217;s look at some sample data, and the expected results when rounded to 1 decimal place:

<pre>Value     Up    Down  Towards Zero  Away From Zero  Bankers  Stochastic
--------  ----  ----  ------------  --------------  -------  ----------
 4.15      4.2   4.1   4.1            4.2            4.2       4.1 or 4.2
 4.15001   4.2   4.2   4.2            4.2            4.2       4.2
 4.25      4.3   4.2   4.2            4.3            4.2       4.2 or 4.3
-4.15     -4.1  -4.2  -4.1           -4.2           -4.2      -4.1 or -4.2
-4.25     -4.2  -4.3  -4.2           -4.3           -4.2      -4.2 or -4.3
-4.25001  -4.3  -4.3  -4.3           -4.3           -4.3      -4.3
</pre>

**Round Up:**
  
This method always rounds the number up to the next highest number. For example, 3.1 rounded up is 4 and -3.1 rounded up is -3. In SQL Server, the easiest way to achieve ‘RoundUp’ is to use the ceiling function. Unfortunately, the Ceiling function only works with whole numbers. There’s no built-in function that RoundsUp where you can specify the number of digits. This is no problem, we can just build our own.

sql
Create Function dbo.RoundUp(@Val Decimal(32,16), @Digits Int)
Returns Decimal(32,16)
AS
Begin
    Return Case When Abs(@Val - Round(@Val, @Digits, 1)) * Power(10, @Digits+1) = 5 
                Then Ceiling(@Val * Power(10,@Digits))/Power(10, @Digits)
                Else Round(@Val, @Digits)
                End
End
```
**Round Down:**
  
This method is similar to rounding up, except it is rounded to the nearest smaller number. For example, 3.1 rounded down is 3, -3.1 rounded down is -4. SQL Server has a floor function that rounds down for up. Just like the ceiling function, the floor function does not accommodate the number of digits (it only works on whole numbers). Another function to the rescue.

sql
Create Function dbo.RoundDown(@Val Decimal(32,16), @Digits Int)
Returns Decimal(32,16)
AS
Begin
    Return Case When Abs(@Val - Round(@Val, @Digits, 1)) * Power(10, @Digits+1) = 5 
                Then Floor(@Val * Power(10,@Digits))/Power(10, @Digits)
                Else Round(@Val, @Digits)
                End
End
```
**Round Away From Zero:**
  
This method does just that. 3.5 rounds away from zero to 4. -3.5 rounds away from zero to -4. Implementing Round Away From Zero is the simplest one to accommodate in SQL Server. The ROUND function does this already, and it accommodates a varying number of digits, too.

**Round Towards Zero:**
  
In this method, 3.5 rounds down to 3, but -3.5 rounds up to -3. Implementing Round Towards Zero is relatively simple in SQL Server too. There is an optional 3rd parameter to the Round function that simply truncates the number to the number of decimal places. Unfortunately, this does not check for the edge case where the last digit is 5 followed by zeros. 

4.15 should round to 4.1
  
4.15000001 should round to 4.2

Using the 3rd argument of the round function will blindly truncate the trailing numbers, so both would be rounded to 4.1. This is not what we want, so we’ll need to write another function.

sql
Create Function dbo.RoundToZero(@Val Decimal(32,16), @Digits Int)
Returns Decimal(32,16)
AS
Begin
    Return Case When Abs(@Val - Round(@Val, @Digits, 1)) * Power(10, @Digits+1) = 5 
                Then Round(@Val, @Digits, 1)
                Else Round(@Val, @Digits)
                End
End
```
**Round Toward Even:**
  
This is known by many different names (Bankers Rounding, unbiased rounding, Gaussian rounding, and statisticians rounding). With this method, special exceptions are made if the last digit to remove is exactly 5. If it is, the number is rounded to the next even number.
  
Example,
  
4.15 Bankers Rounded to 1 digits is 4.2
  
4.25 Bankers Rounded to 1 digits is 4.2

This occurs because everything to the right of the number of digits (5) is exactly 5. Had it been 51, the number would have been rounded up. Less than 50, and the number is rounded down. Since it is exactly 50, we need to examine the digit to the left of it (the 1st decimal place). If it’s an even number, the rounded value is truncated. If it’s an odd number (like the 1 in 4.15), then it’s rounded to the next even number (to become 4.2). 

We can write another function in SQL Server to implement bankers rounding.

sql
Create Function dbo.BankersRound(@Val Decimal(32,16), @Digits Int)
Returns Decimal(32,16)
AS
Begin
    Return Case When Abs(@Val - Round(@Val, @Digits, 1)) * Power(10, @Digits+1) = 5 
                Then Round(@Val, @Digits, Case When Convert(int, Round(abs(@Val) * power(10,@Digits), 0, 1)) % 2 = 1 Then 0 Else 1 End)
                Else Round(@Val, @Digits)
                End
End
```
**Stochastic Rounding**
  
This is similar to &#8216;Round to even&#8217;. When the remaining digits is a 5 followed by zeros, we randomly round down or up. Implementing this is a bit more difficult because SQL Server doesn&#8217;t like random numbers in user defined function. To work-around this limitation, we can create a view that returns a random number and call that view from within the function. First, the view:

sql
Create View vw_RandomBit
As
Select Case When Convert(Char(1), Convert(VarChar(36), NewId())) > '7' 
            Then 1 
            Else 0 
            End As RandomBit
```

Then, we can create this function.

sql
Create Function dbo.StochasticRound(@Val Decimal(32,16), @Digits Int)
Returns Decimal(32,16)
AS
Begin
    Return Case When Abs(@Val - Round(@Val, @Digits, 1)) * Power(10, @Digits+1) = 5 
                Then Round(@Val, @Digits, Convert(int, (Select	RandomBit From vw_RandomBit)))
                Else Round(@Val, @Digits)
                End
End
```
I hope the differences in the algorithm is obvious now, and you&#8217;ll be able to make a better informed choice regarding which method of rounding to use.