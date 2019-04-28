---
title: 'Nancy and the mystery of the 405: method not allowed'
author: Christiaan Baes (chrissie1)
type: post
date: 2015-03-18T11:48:17+00:00
ID: 3309
url: /index.php/uncategorized/nancy-and-the-mystery-of-the-405-method-not-allowed/
views:
  - 1292
categories:
  - Uncategorized

---
Yesterday I got a 405: method not allowed on my Put request which I sent to my Nancy powered webservice. I made the request with Javascript. 

For reference this was my module, the routes only.

```vbnet
[Get]("/search") = Function(parameters)
                       'something pretty
                     End Function
Put("/search/{page}") = Function(parameters)
                            'something even more pretty
                          End Function
```
So after I got the method not allowed I went to google and found&#8230; a [post I wrote in the past][1]. So according to my younger self I should blame it on IIS and it not allowing the method. It also had the solution in that post, which was already there in my project.

Mmm, weird. Back to google and searching for the webdav thing and IIS being a bad boy and&#8230; no solution. 

But I remember it working the day before. So when did it stop working? 

Well apparently when I changed the order of the function which contained the call to the service in Javascript.

I had something like this.

```javascript
function search(page) {
        if (typeof page === 'undefined') { page = 1; }
        
        xhr.open("PUT", '/search/' + page);
        ....
 })
```
And I called it like this.

```javascript
search();
```
And I changed it into this.

```javascript
function search(page, searchtext) {
        if (typeof page === 'undefined') { page = 1; }
        
        xhr.open("PUT", '/search/' + page);
        ....
 })
```
And I updated my above call as such (not really, but lets pretend).

```javascript
search("");
```
And that&#8217;s when I got the 405: method not allowed.

So why did I get that?

Because I was now trying to do a PUT on the route /search while it should have been /search/1 which also exists as a route GET. So you are trying to use a route with the wrong verb and then you get a 405 and not a 404, because the route exists and Nancy thinks you are trying the wrong verb. 

So there you go. 

Hope this helps my future self.

 [1]: /index.php/webdev/serverprogramming/nancy-iis-7-and-the/