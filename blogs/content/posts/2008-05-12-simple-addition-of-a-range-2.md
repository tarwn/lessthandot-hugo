---
title: Simple addition of a range
author: damber
type: post
date: 2008-05-12T18:27:44+00:00
ID: 24
url: /index.php/webdev/uidevelopment/javascript/simple-addition-of-a-range-2/
views:
  - 2923
rp4wp_auto_linked:
  - 1
categories:
  - Javascript

---
## Adding a range of numbers

Adding numbers is easy. Very easy for programs, right? How about adding up a range of numbers? 1 to 5 maybe ? 1+2+3+4+5 = 15 … easy !

What about adding up 1 to 100 ? not so easy in your head, but with a little code this shouldn't be a problem. In fact a lot of programmers would approach it like the following:

```javascript
var sumOfRangeLoop = 0;
for (i=1;i<=100;i++)
{
	sumOfRangeLoop += i;
}
document.write("Loop: " + sumOfRangeLoop + "<br/>");	
```
Great – it gets you the answer that you wanted. Now what about 25 to 25,000,000 ? OK, that takes a while to run…. What if I tried 234 to 435,657,123 ? Mmmm… ouch. This doesn't work too well does it ? 

## Improving Performance

If we want to be able to scale our code then we need to use a little math to help us get the answer, instead of relying on brute force. 

In this [excellent and clearly explained article about number range summation][1], you will see that there are many ways to visualise the process to make it easier to calculate the sum of a range – even in our head, and how they all lead toward a single formula. Within the comments you will notice that discussion identifies a more generic formula that allows for the starting position to be more than 1, and also to allow evenly spaced 'steps'. 

```javascript
var rangeStart = 234;
var rangeEnd = 435657123;
var rangeStep = 1;
var digitCount = ((rangeEnd - rangeStart)+rangeStep)/rangeStep;
var sumOfRangeCalc = ((digitCount * (rangeEnd+rangeStart) ) / 2);	
document.write("Sum of Range: " + sumOfRangeCalc + "<br/>");	
```
This runs almost instantly, compared to the hung browser effect of the loop approach for this scale of numbers. 

## Performance Benchmarks

To see just how different the performance is, here is a complete working example testing both methods over multiple iterations of the same moderately sized range

```javascript
<html>
<head>
<title>test</test>
</head>
<body>
<script type="text/javascript">

	var rangeStart = 150;
	var rangeEnd = 25000;
	var rangeStep = 1;
	var iterations = 1000;
	var ts;
	var te;

	
	document.write("Testing Calc:  ");
	ts = new Date();
	for (j=0;j<iterations;j++)
	{
		sumByCalc();
	}
	te = new Date();
	document.write(((te - ts)/1000) + " seconds<br/>");

	document.write("Testing Loop:  ");
	ts = new Date();
	for (j=0;j<iterations;j++)
	{
		sumByLoop();
	}
	te = new Date();
	document.write(((te - ts)/1000) + " seconds<br/>");

	
	function sumByCalc()
	{
		var digitCount = ((rangeEnd - rangeStart)+rangeStep)/rangeStep;
		var sumOfRangeCalc = ((digitCount * (rangeEnd+rangeStart) ) / 2);	
//		document.write("Equation: " + sumOfRangeCalc + "<br/>");	
	}

	function sumByLoop()
	{
		var sumOfRangeLoop = 0;
		for (i=rangeStart;i<=rangeEnd;i+=rangeStep)
		{
			sumOfRangeLoop += i;
		}
//		document.write("Loop: " + sumOfRangeLoop + "<br/>");	
	}
		
</script>
</body>
</html>
```
##### Results:

<pre>Testing Calc: 0.001 seconds
Testing Loop: 7.342 seconds
</pre>

## In Conclusion

So, this simply means that you should use the right tool for the job. The Brute Force method doesn't scale very well – there are simple equations that can answer this question, so.. use them. Here is a self contained function that returns the sum of a range from x to y with stepping z. You'll need to validate the rangeStep to ensure it is valid with the rangeStart and rangeEnd.

```javascript
function sumRange(rangeStart, rangeEnd, rangeStep)
	{
		var digitCount = ((rangeEnd - rangeStart)+rangeStep)/rangeStep;
		var sumOfRangeCalc = ((digitCount * (rangeEnd+rangeStart) ) / 2);	
		return sumOfRangeCalc;
	}
```

 [1]: http://betterexplained.com/articles/techniques-for-adding-the-numbers-1-to-100/