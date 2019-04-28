---
title: Kotlin the data class and the class
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-24T06:41:00+00:00
ID: 1798
excerpt: Exploring the data class in kotlin.
url: /index.php/desktopdev/suntech/java/kotlin-the-data-class-and/
views:
  - 7494
categories:
  - Java
tags:
  - class
  - data class
  - kotlin

---
## Introduction

Over the last few weeks I have been playing around with [Kotlin][1]. 

First let me tell you that it can be a bit frustrating because Kotlin is far from a finished product. It works on an EAP version of IntelliJ with nightly builds of the plugin. So you are bound to have version that don&#8217;t work. I had 2 of those over the last week. But it now works with [IntelliJ 123.4][2] and kotlin plugin [0.4.214][3].

And that things still change can be proven by the fact that the samples use tuples while tuples have been removed and replaced by data classes. So you can expect to find errors in the documentation and in the samples on github. 

But we are here to learn. And I found the concept of the data class very intriguing.

## The class

in kotlin you can define a class like this.

```java
class MyClass(val i : Int, val s : String)```
That&#8217;s a class with two properties named i and s.

And we can read out all the usual methods like this.

```java
fun main(args : Array&lt;String&gt;) {
    val c1 = MyClass(1,"t")
    val c2 = MyClass(1,"t")
    val c3 = MyClass(2,"t")
    println(c1.toString())
    println(c1.equals(c2))
    println(c1.equals(c3))
    println(c1.equals(c1))
    println(c1.hashCode())
    println(c2.hashCode())
    println(c3.hashCode())
    println(c1.i)
    println(c1.s)
}```
with this as the result.

> demo.MyClass@337d0f
  
> false
  
> false
  
> true
  
> 3374351
  
> 21356612
  
> 8831815
  
> 1
  
> t

That&#8217;s how I would expect any class to behave even in .Net and Java. Nothing special.

## The data class

Now lets add the keyword data in front of it.

```java
data class MyClass(val i : Int, val s : String)```
and run our code again.

> MyClass(i=1, s=t)
  
> true
  
> false
  
> true
  
> 147
  
> 147
  
> 178
  
> 1
  
> t

the result is completely different. c1 and c2 are the same the tostring is differnt and the hashcodes are different.

The data class behaves as a DTO.

But the data class is also a tupple.

Which we can demonstrate by adding these two lines to our main.

```java
println(c1.component1())
    println(c1.component2())```
Which will now give us the following output.

> MyClass(i=1, s=t)
  
> true
  
> false
  
> true
  
> 147
  
> 147
  
> 178
  
> 1
  
> t
  
> 1
  
> t

Just like a tupple.

So tostring, equals and hashcode are autoimplemented for a data class and based on the properties you provide. 

But you can also override that behavior if you want.

```java
data class MyClass(val i : Int, val s : String)
{
    fun equals(val obj : Any) : Boolean {
        return (obj as MyClass).s.equals(s)
    }

    fun hashCode() : Int
    {
        return s.hashCode()
    }

    fun toString() : String {
        return s.toString()
    }
}```
with this as the result.

> t
  
> true
  
> true
  
> true
  
> 116
  
> 116
  
> 116
  
> 1
  
> t
  
> 1
  
> t 

Cool.

## Conclusion

I very much like the data class and it would come in handy in many other languages.

 [1]: http://confluence.jetbrains.net/display/Kotlin/Getting+Started
 [2]: http://eap.jetbrains.com/idea
 [3]: http://teamcity.jetbrains.com/viewType.html?tab=buildTypeStatusDiv&buildTypeId=bt345&guest=1