---
title: 'Scala: The difference between val and var'
author: SQLDenis
type: post
date: 2013-01-12T10:25:00+00:00
excerpt: 'This is a short Scala post to explain what the difference is between val and var. I was showing some Scala code to a co-worker this past week and he was asking what the difference was between val and var. It is quite simple:'
url: /index.php/enterprisedev/appserver/jee/scala-the-difference-between-val/
views:
  - 7149
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
  - Java EE
tags:
  - functional programming
  - java
  - jvm
  - polyglot
  - scala

---
This is a short Scala post to explain what the difference is between val and var. I was showing some Scala code to a co-worker this past week and he was asking what the difference was between val and var. It is quite simple:

**val** defines a fixed value, it is a read only variable
  
**var** defines a mutable variable, this variable can be modified

In Java you would use final to create a variable which would be read only, this is the same as val in Scala. 

Let&#8217;s look at some very simple Scala code.

<pre>object Test {

  def main(args: Array[String]) = println("done with main")
  
var Test1 =5
println("Test1 " + Test1)
  
Test1 = 6
println("Test1 " + Test1)

}</pre>

Running the code above will give the following output
  
Test1 5
  
Test1 6
  
done with main

If you are trying to use val, you will get an error, change var to val and see if you can compile the code

<pre>object Test {

  def main(args: Array[String]) = println("done with main")
  
val Test1 =5
println("Test1 " + Test1)
  
Test1 = 6
println("Test1 " + Test1)

}</pre>

Here is the error, the code won&#8217;t even compile

<pre>Description		Resource	Path		Location	Type
reassignment to val	Test.scala	/ScalaTemp/src	line 8		Scala Problem</pre>

So as you can see, val is read only, while with var you can modify the variable.

If you want to play around with Scala, take a look at [Installing Scala 2.10 on Eclipse Juno][1]

 [1]: /index.php/EnterpriseDev/AppServer/JEE/installing-scala-2-10-on