---
title: Trying out node.js
author: Christiaan Baes (chrissie1)
type: post
date: 2011-06-13T06:19:00+00:00
ID: 1215
excerpt: Node.js is kind of a new kid on the block. You might have heard about it since it uses javascript and in the future everything will be using javascript. node.js is server-side javascript.
url: /index.php/webdev/uidevelopment/javascript/trying-out-node-js/
views:
  - 5087
categories:
  - Javascript
tags:
  - node.js
  - ubuntu
  - virtualbox

---
## Introduction

Node.js is kind of a new kid on the block. You might have heard about it since it uses javascript and in the future everything will be using javascript. node.js is server-side javascript. And you can read what the point is [on their website][1].

## Setting it up

node.js is coming from the linux world so the best way to test it and play with it is to install [virtualbox][2] and then download a [Ubuntu VM][3]. I downloaded version 11.04. Best is to also install the Virtualbox extensions.

Then if you are ready open a terminal in Ubuntu and type in the following lines to get node installed. 

> sudo apt-get install python-software-properties
  
> sudo add-apt-repository ppa:jerome-etienne/neoip
  
> sudo apt-get update
  
> sudo apt-get install nodejs

it might take a few minutes to get everything installed and updated. From now on in you should always have the latest version of node since the package-manager will take care of that. You must love linux for that (you can hate it for other reasons). 

That was easy and should take about 20 minutes.

## Our first baby

The first thing to do is to try out the hello world application that the node.js site suggests.

```javascript
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello Worldn');
}).listen(1337, "127.0.0.1");
console.log('Server running at http://127.0.0.1:1337/');```
for this I went to my Documents place and added a new empty document.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nodejs/nodejs1.png?mtime=1307949854"><img alt="" src="/wp-content/uploads/users/chrissie1/nodejs/nodejs1.png?mtime=1307949854" width="1070" height="879" /></a>
</div>

and the name the file helloworld.js. Copy the code you see above in it and save it.

Now go back to the terminal and go to the Documents directory and type in <code class="codespan">node helloworld.js </code>. And this will be the result.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nodejs/nodejs2.png?mtime=1307950076"><img alt="" src="/wp-content/uploads/users/chrissie1/nodejs/nodejs2.png?mtime=1307950076" width="1070" height="879" /></a>
</div>

As you can see I have also opened firefox and surfed to http://127.0.0.1:1337/ because that is where the script said it was going to be on the line <code class="codespan">.listen(1337, "127.0.0.1");</code> on the lines 

```javascript
res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello Worldn');```
we told it to return something with a content type of text and a content of Hello world.

Of course all this is nothing and has nothing to do with the real world. 

I would therefor highly recommend you read up on node.js in [The node beginner book][4] it makes it easy to understand an gets you going quickly.

Some more references.

  * [Taking baby steps with node.js][5]
  * [Learning Server-Side JavaScript with Node.js][6]
  * [node tuts][7]
  * [Felix&#8217;s Node.js Guide][8]

## Conclusion

I very much like where this is going.node.js is far from mature but the basic principles are sound and since javascript seems to be the future it is best to start learning now, but not only via things like node.js but also other things. I&#8217;m sure there will also be pitfalls and bad things about it, like a lack of tooling for the moment, but we will get over it. I would however not try this in windows, although it works doing it in linux is so much more fun and very easy and fast to set up.

 [1]: http://nodejs.org/docs/v0.4.8/#about
 [2]: http://www.virtualbox.org/wiki/Downloads
 [3]: http://virtualboxes.org/images/ubuntu/
 [4]: http://www.nodebeginner.org/#head12
 [5]: http://elegantcode.com/category/node-js/
 [6]: http://net.tutsplus.com/tutorials/javascript-ajax/learning-serverside-javascript-with-node-js/
 [7]: http://nodetuts.com/
 [8]: http://nodeguide.com/