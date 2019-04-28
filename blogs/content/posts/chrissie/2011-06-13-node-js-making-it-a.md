---
title: node.js making it a bit more readable
author: Christiaan Baes (chrissie1)
type: post
date: 2011-06-13T10:12:00+00:00
ID: 1217
excerpt: "Let's be honest the default Hello world code is far from readable and there is a bit of a mix of concerns there. So let's make it better."
url: /index.php/webdev/uidevelopment/javascript/node-js-making-it-a/
views:
  - 3226
categories:
  - Javascript
tags:
  - node.js

---
## Introduction

Let&#8217;s be honest the default Hello world code is far from readable and there is a bit of a mix of concerns there.

```javascript
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello Worldn');
}).listen(1337, "127.0.0.1");
console.log('Server running at &lt;a href="http://127.0.0.1:1337/');"&gt;http://127.0.0.1:1337/');&lt;/a&gt;```
So let&#8217;s split things up.

## Splitting it up

First of all I want to split it up in a server class and a helloworld class. The server class makes the server and the helloworld class will handle the making of the page.

If we would do extract method I would end up with two more functions. One to start the function and one to make the helloworld page.

```javascript
var http = require('http');

http.createServer(start).listen(1337, "127.0.0.1");

function start (req, res) {
  helloworld(res);
}

function helloworld(response)
{
  response.writeHead(200, {'Content-Type': 'text/plain'});
  response.end('Hello Worldn');
}

console.log('Server running at http://127.0.0.1:1337/');```
Now that function helloworld would not belong in there so we would copy past it over to another file, namely helloworld.js.

Now we just need our server.js to know where it should look for that function.

```javascript
var http = require('http');
var helloworld = require('./helloworld');

http.createServer(start).listen(1337, "127.0.0.1");

function start (req, res) {
  helloworld.helloworld(res);
}

console.log('Server running at http://127.0.0.1:1337/');```
Do you see the require and how we put that in a variable so we can access it? Looks a bit like the require in PHP but it works differently. 

Now we can look at our helloworld.js file.

```javascript
function helloworld(response)
{
  response.writeHead(200, {'Content-Type': 'text/plain'});
  response.end('Hello Worldn');
}

exports.helloworld = helloworld;```
look at the exports line. I guess you could compare this to setting a function public in C#. I think that is an odd way of doing things, but I guess you get used to it. 

## Conclusion

I guess I now have a simpler and more readable hello world. Which is the beginning to bigger and better things. The result is still the same. It&#8217;s just different. You should not forget that the same rules apply for all languages. Separate your concerns as best as possible and do the things there where they make most sense. The anonymous functions you saw in the first example can easily lead to more spaghetti code, be careful with that. Someone else will have to read your crap afterwards.