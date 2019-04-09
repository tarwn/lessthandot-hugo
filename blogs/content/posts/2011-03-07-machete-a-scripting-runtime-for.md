---
title: Machete – A scripting runtime for .NET
author: chaospandion
type: post
date: 2011-03-07T20:21:00+00:00
ID: 1069
excerpt: |
  In an attempt to overcome by perfectionism I've decided to open source my long term side project Machete for the world to see. Machete is my own dialect of the ECMAScript 5 standard or as it is more commonly called JavaScript. 
  
  
    The compiler is wr&hellip;
url: /index.php/desktopdev/mstech/machete-a-scripting-runtime-for/
views:
  - 20618
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - .net
  - 'c#'
  - ecmascript
  - 'f#'
  - javascript
  - machete
  - scripting

---
In an attempt to overcome my perfectionism I've decided to open source my long term side project Machete for the world to see. Machete is my own dialect of the ECMAScript 5 standard or as it is more commonly called JavaScript.

# Features

Cleaner lambda expressions:

<pre style="background-color:#EEEEEE;padding:5px;"><code>var succinct = (x, y) x + y;
var verbose = function (x, y) { return x + y; };
</code></pre>

First class iteration support with the foreach loop and generators.

<pre style="background-color:#EEEEEE;padding:5px;"><code>var numbers = generator {
    yield 1;
    yield 2;
    yield 3;
};

foreach (var n in numbers) {
    Output.write(n);
}   

foreach (var e in ["Array", " objects", " are", " iterable", "!"]) {
    Output.write(e);
} 

foreach (var c in "Strings are iterable!") {
    Output.write(c);
}
</code></pre>

<h1 style="margin-bottom:10px;margin-top:15px">
  Implementation
</h1>

  * The compiler is written in F# and uses the library [FParsec][1].
  * The runtime is written in C# and is hosted by .NET.
  * It currently has over 400 tests with many more on the way.

Machete is the product of almost a years worth of research, design, and coding. I have it hosted on GitHub so please stop by and fork the project. I would really love to up my test count dramatically and test cases from the community would be invaluable. Without further ado, the link to my repository.

[GitHub Repository For Machete][2]

 [1]: https://bitbucket.org/fparsec/main/overview
 [2]: https://github.com/ChaosPandion/Machete