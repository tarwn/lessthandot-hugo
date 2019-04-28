---
title: 'Kotlin: val and var'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-25T06:46:00+00:00
ID: 1799
excerpt: Trying out more of kotlin and exploring the difference between val and var.
url: /index.php/desktopdev/suntech/java/kotlin-val-and-var/
views:
  - 5516
categories:
  - Java
tags:
  - kotlin
  - val
  - var

---
## Introduction

I am now using kotlin plugin 0.4.221 while I was using 0.4.214 just yesterday.

In the last couple of weeks I started dabbling in kotlin a bit and wrote [a first blogpost about the data class,][1] because the data class was a new concept that I have not yet encountered in one of the many programminglanguages I have used over the years. But that probably means I have just been using the wrong ones all these years. Another new concept for me was the val keyword. 

The val keyword tells the compiler that our object is immutable.

## To val or not to val

So let&#8217;s see what that means.

Let&#8217;s make a data class with a val property and a var property.

```java
data class MyClass(var i : Int, val s : String)
```
And now let&#8217;s try and use our class, like this.

```java
fun main(args : Array&lt;String&gt;) {
    val c1 = MyClass(1,"t")
    c1.i = 2
    c1.s = "s"
}```
you will get an error on the <code class="codespan">c1.s = "s"</code> line because s is immutable and has been set in the constructor.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/kotlin/kotlin1.png?mtime=1353831918"><img alt="" src="/wp-content/uploads/users/chrissie1/kotlin/kotlin1.png?mtime=1353831918" width="279" height="124" /></a>
</div>

You might have noticed that we instantiated our class with the val keyword as well. this means you can no longer assign another instance to it..

So when I try this.

```java
c1 = MyClass(1,"s")```
I will get the same error as before.

So if you want to change an immutable class then you will have to create another instance and start passing that along.

```java
data class MyClass(val i : Int, val s : String)

fun main(args : Array&lt;String&gt;) {
    val c1 = MyClass(1,"t")
    println(c1.i)
    println(c1.s)
    val c2 = MyClass(c1.i,"s")
    println(c2.i)
    println(c2.s)
}```
## Conclusion

Immutability is desirable a lot of the time, it protects you from unwanted side effects. 

[According to wikipedia.][2]

> Immutable objects are often useful because they are inherently thread-safe.[1] Other benefits are that they are simpler to understand and reason about and offer higher security than mutable objects.

 [1]: /index.php/DesktopDev/SunTech/Java/kotlin-the-data-class-and
 [2]: http://en.wikipedia.org/wiki/Immutable_object