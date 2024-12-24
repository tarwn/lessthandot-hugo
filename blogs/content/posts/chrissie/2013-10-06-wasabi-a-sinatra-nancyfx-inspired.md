---
title: Wasabi a sinatra/nancyfx inspired webframework for kotlin
author: Christiaan Baes (chrissie1)
type: post
date: 2013-10-06T07:46:00+00:00
ID: 2172
excerpt: Wasabi a sinatra/nancyfx inspired webframework for kotlin. As the title seems to imply.
url: /index.php/webdev/serverprogramming/wasabi-a-sinatra-nancyfx-inspired/
views:
  - 13296
categories:
  - Server Programming
tags:
  - kotlin
  - wasabi

---
## Introduction

[Wasabi][1] is a sinatra inspired webframework for kotlin.

Or according to the author.

> An HTTP Framework built with Kotlin for the JVM.
> 
> Wasabi combines the conciseness and expressiveness of Kotlin, the power of Netty and the simplicity of Express.js (and other Sinatra-inspired web frameworks) to provide an easy to use HTTP framework

Wasabi has no viewengine. You should use a clientside MVC-framework like Angularjs or ember.js.

So on to the code.

## Installation

There is a gradle file that should import everything for you. But I&#8217;m stupid and couldn&#8217;t get it to work. So I just cloned the repo and then added that as a module to my intellij project. 

## Hello world

This is the absolute minimum code I have found to get wasabi to show me anything. No matter what the read.me says.

```java
package be.baes

import org.wasabi.app.AppServer

fun main(args: Array&lt;String&gt;) {

    val server = AppServer()

    server.get("/", { response.send ("Hello world.") })

    server.start()

}
```
You should now see this message in the run window of your intellij project.

> [main] INFO org.wasabi.app.AppServer &#8211; Server starting on port 3000

We can now assume that the url will be http:\localhost:3000

And yes, yes it does.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/wasabi/wasabi1.png?mtime=1381044543"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/wasabi/wasabi1.png?mtime=1381044543" width="267" height="163" /></a>
</div>

You should also see these mentions in your run window in intellij.

> [nioEventLoopGroup-2-1] INFO org.wasabi.interceptors.LoggingInterceptor &#8211; [GET] &#8211; /
  
> [nioEventLoopGroup-2-2] INFO org.wasabi.interceptors.LoggingInterceptor &#8211; [GET] &#8211; /favicon.ico 

So how do get it to output to another port?

Easy, we give the AppServer object a configuration object.

```java
val config = AppConfiguration(4000, "This is me on port 4000", true)
    val server = AppServer(config)
    ```
Clearly the first parameter is the port, the second a message when connecting and the third is the option to turn off logging.

Now let&#8217;s try and return an object.

```java
package be.baes

import org.wasabi.app.AppServer
import org.wasabi.app.AppConfiguration
import org.wasabi.interceptors.parseContentNegotiationHeaders
import org.wasabi.interceptors.negotiateContent

fun main(args: Array&lt;String&gt;) {

    val config = AppConfiguration(4000, "This is me on port 4000", true)
    val server = AppServer(config)
    
    var person = Person()
    person.firstName = "test"
    person.lastName = "test2"
    server.get("/", { response.send (person) })

    server.start()

}

class Person()
{
    public var lastName : String = ""
    public var firstName : String = ""
}
```
And when we try the above with postman we get&#8230;.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/wasabi/wasabi2.png?mtime=1381045161"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/wasabi/wasabi2.png?mtime=1381045161" width="543" height="473" /></a>
</div>

NOTHING.

So I must be missing something.

```java
package be.baes

import org.wasabi.app.AppServer
import org.wasabi.app.AppConfiguration
import org.wasabi.interceptors.parseContentNegotiationHeaders
import org.wasabi.interceptors.negotiateContent

fun main(args: Array&lt;String&gt;) {

    val config = AppConfiguration(4000, "This is me on port 4000", true)
    val server = AppServer(config)
    server.negotiateContent()

    var person = Person()
    person.firstName = "test"
    person.lastName = "test2"
    server.get("/", { response.send (person) })

    server.start()

}

class Person()
{
    public var lastName : String = ""
    public var firstName : String = ""
}
```
I need to tell it to negotiateContent. I kinda think that should be by default.

And now we get.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/wasabi/wasabi3.png?mtime=1381045351"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/wasabi/wasabi3.png?mtime=1381045351" width="462" height="402" /></a>
</div>

Wasabi only supports json for now. No XML, no HTML. 

<span class="MT_red">Update</span>

Considering the comment by Hadi, the more concise form would then look like.

```java
package be.baes

import org.wasabi.app.AppServer
import org.wasabi.app.AppConfiguration
import org.wasabi.interceptors.parseContentNegotiationHeaders
import org.wasabi.interceptors.negotiateContent

fun main(args: Array&lt;String&gt;) {

    val config = AppConfiguration(4000, "This is me on port 4000", true)
    val server = AppServer(config)
    server.negotiateContent()

    var person = Person("test","test2")
    server.get("/", { response.send (person) })

    server.start()

}

class Person(var firstName: String, var lastName: String)
```
## Conclusion

It needs some more work but it works and isn&#8217;t to hard to get to work. Go ahead and play with it.

 [1]: https://github.com/hhariri/wasabi