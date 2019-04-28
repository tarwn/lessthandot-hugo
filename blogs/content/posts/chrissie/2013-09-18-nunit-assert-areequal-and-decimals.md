---
title: NUnit Assert.AreEqual and decimals and doubles
author: Christiaan Baes (chrissie1)
type: post
date: 2013-09-18T07:38:00+00:00
ID: 2160
excerpt: Implicit conversions are evil and you should watch out for them.
url: /index.php/desktopdev/mstech/nunit-assert-areequal-and-decimals/
views:
  - 15564
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - nunit
  - vb.net

---
When working with numbers with high precision be very carefull to not be caught by the default behavior of.Net.

Let us take a simple case.

```vbnet
Assert.AreEqual(0.100000000000001, 0.1000000000000010000000000001D)```
Nunit will say that the above test passes. 

While it is obvious to us that it shouldn&#8217;t. 

It is however less apparent when doing something like this.

```
Assert.AreEqual(0.100000000000001, sut.DecimalProperty)```
This happens because the VB compiler wil try to find the best signature for the AreEqual method that fits the parameters that you are trying to give it. And while doing that it makes an implicit conversion of the Decimal to a double. Probably because the first parameter is a double. 

So if you are doing high precision calculations and veryfing if they are correct make sure that you use the correct datatype. 

As soon as we do this.

```vbnet
Assert.AreEqual(0.100000000000001D, 0.1000000000000010000000000001D)```
The test will fail.

This doesn&#8217;t only happen when testing of course this also happens when comparing. Implicit data conversion can be handy sometimes but are evil in most other cases.