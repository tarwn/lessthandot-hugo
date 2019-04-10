---
title: SQL Server efficient handling of divide by zero
author: George Mastros (gmmastros)
type: post
date: 2008-11-25T16:01:00+00:00
ID: 220
excerpt: "There are various methods to accommodate this problem, let's examine a few of them and also check performance.  When you divide two numbers, and the denominator is 0, the result of the operation is undefined.  In reality, we usually define some alternat&hellip;"
url: /index.php/datamgmt/datadesign/sql-server-efficient-handling-of-divide/
views:
  - 15847
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
There are various methods to accommodate this problem, let's examine a few of them and also check performance. When you divide two numbers, and the denominator is 0, the result of the operation is undefined. In reality, we usually define some alternative number to use (usually 0). In the example and the code I show below, I will assume that the expected result for a 'divide by zero' condition is 0.

For most of us, the typical method for accommodating divide by zero is to first check the denominator to see if it is equal to zero. If it is, then return 0, otherwise perform the division. Like this…

sql
Select Case When Denominator = 0
            Then 0             
            Else Numerator/Denominator             
            End As [CalculatedColumn]
```


Most programmers are very familiar with this syntax, and often times, it's the best way to approach the problem. However, there is another method that can be better under the right circumstances.

The problem occurs when the denominator is a calculated value, and that calculation is slow.

sql
Select Case When dbo.SlowPerformingFunction() = 0
            Then 0
            Else Numerator / dbo.SlowPerformingFunction()
            End As [CalculatedColumn]
```


In this case, the SlowPerformingFunction is evaluated twice, once in the test and again in the division. There is a way to write this query so that the function is only evaluated once. In order to understand this method, we need to know about other functions.

SQL Server has a NULLIF function. This function accepts 2 parameters. If the first parameter is equal to the second parameter, NULL is returned. Otherwise, the value in the first parameter is returned. EX:

sql
Select NullIf(0,0) -- Returns NULL
Select NullIf(7,0) -- Returns 7
```


Another handy function to know is COALESCE. Coalesce accepts multiple parameters. It checks each parameter and returns the first one that is NOT NULL. If all parameters are NULL, NULL is returned. Ex:

sql
Select Coalesce(NULL, NULL, NULL, 'Apple') -- Returns Apple
```


Lastly, we need to think about the division. If you try to divide any number by zero, you will get an error. However, if you divide a number by NULL, you get NULL. We can use this to our advantage to prevent the 'divide by zero' error while evaluating slow performing functions just once. Like this:

sql
Select Coalesce(Numerator / NullIf(dbo.SlowPerformingFunction(), 0), 0) As [CalculatedColumn]
```


In this case, NULLIF will return the value from the SlowPerformingFunction if that value is not 0. If it is, NULLIF will return NULL. We then perform the calculation, and if the result is NULL, we replace it with 0 using the Coalesce function.

**Is this method actually faster?**

The true answer is, 'It depends'. If your denominator is a scalar value (parameter to the procedure or a pre-calculated value), then NO. The Case/When method can be faster. You see, if the denominator is zero, the division does not occur. However, if you have a poorly performing function, then the Coalesce/NullIf method will be faster because the function is called only once. Sure, the division occurs even if the result of the function is 0, but it's NULL math, and a simple division too. My recommendation is to write the query both ways and compare the performance. Whichever method performs better is the one you should use.