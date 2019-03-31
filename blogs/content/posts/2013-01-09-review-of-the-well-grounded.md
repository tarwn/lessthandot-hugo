---
title: Review of The Well-Grounded Java Developer
author: SQLDenis
type: post
date: 2013-01-09T15:12:00+00:00
excerpt: |
  This is a review of The Well-Grounded Java Developer, Vital techniques of Java 7 and polyglot programming. Written by Benjamin J. Evans and Martijn Verburg. The book was published in July, 2012 and it contains 496 pages
  
  This is an excellent&hellip;
url: /index.php/enterprisedev/appserver/jee/review-of-the-well-grounded/
views:
  - 29148
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
  - Java EE
tags:
  - book
  - book review
  - groovy
  - java functional programming
  - jvm
  - maven
  - polyglot
  - scala

---
This is a review of [The Well-Grounded Java Developer, Vital techniques of Java 7 and polyglot programming][1]. Written by Benjamin J. Evans and Martijn Verburg. The book was published in July, 2012 and it contains 496 pages

[<img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/evans_cover150.jpg?mtime=1357748948" width="150" height="188" style="float:left;margin:0 5px 0 0;" />][2]Right from the start I want to say that this is an excellent book and I highly recommend it. One thing you do need to be aware about is that in order to use this book you need to know Java. If you don&#8217;t know Java then this is not the book for you. This book is all about bringing you to the next level as a Java developer by showing you how to do continuous integration, dependency injection, testing, performance tuning, and more.
  
This book even shows you how to use different languages that run on the JVM. The book is written in a easy and concise style, everything is very clear. I also love the annotations which are sprinkled throughout the book, they give some nice background information. While the chapters on Groovy, Scala and Clojure are not a complete reference for the language, they do provide enough material to form a nice foundation, now you can further explore the language on your own.

The book is split up in four parts, I will list each part with the chapters within that part and will give a brief summary what the part is about

**Part 1 Developing with Java 7**
  
Chapter 1 Introducing Java 7
  
Chapter 2 New I/O

This section shows you what was added in Java 7, for example you can now use strings in switch statements, try-with-resources(similar to a using statement in c#, it frees up the resources after it is done). The Java IO stuff has been rewritten and much easier to use, where you had to write a whole bunch of code before, you can now do the same in much less code. Some things that did not exist in Java in terms of IO but now does exist are explained as well.

**Part 2 Vital techniques**
  
Chapter 3 Dependency Injection
  
Chapter 4 Modern concurrency
  
Chapter 5 Class files and bytecode
  
Chapter 6 Understanding performance tuning

This part has a lot of stuff and as a Java developer you should really know how this stuff works if you want to get to the next level. Dependency Injection(DI) and inversion of control (IoC) are covered, Guice 3, the reference implementation for DI in Java is also covered. Concurrency before Java 5 and concurrency now is covered. Concurrency will be a must now that we have multi-CPU and multi-core everywhere, you better get your wits around it. There is a whole chapter on the class files itself and how they are loaded as well as what they compile into. These are fun details and will show you exactly what happens when you compile and execute a class. Evereybody&#8217;s favorite subject performance tuning is covered in this part of the book as well.

**Part 3 Polyglot programming on the JVM**
  
Chapter 7 Alternative JVM languages
  
Chapter 8 Groovy: Java’s dynamic friend
  
Chapter 9 Scala: powerful and concise
  
Chapter 10 Clojure: safer programming

This is a very interesting part of the book and I would suggest not to skip it. There are 3 type of languages covered in this part:
  
Groovy, a dynamic language
  
Scala, a functional language
  
Clojure, a Lisp for functional programming

Take a look at these languages and you will be amazed how much Java boilerplate code you can eliminate by using these languages instead. Some of the functional is a little bit of a paradigm shift and you might need some time adjusting. You will also see how you can interoperate between these languages and Java.

**Part 4 Crafting the polyglot project**
  
Chapter 11 Test-driven development
  
Chapter 12 Build and continuous integration
  
Chapter 13 Rapid web development
  
Chapter 14 Staying well-grounded

The last part is all about automation and making your life easier. If you are a developer who still deploys stuff by using FTP to move JAR, EAR and WAR files, pay attention. Maven is covered as the build automation tool, Jenkins is the continuous integration tool. In the Rapid web development chapter Grails is explored.

* * *Let me just repeat again that I think this is an awesome book and as a Java developer you have to check it out. The one thing that is missing from the book is ORM, it is covered a little in the testing chapter but if you want to know about ORM, you will need to pick out some other book just for that.</p> 

You can download the following chapters to get a feel for the book

[Sample chapter 1][3]
  
[Sample chapter 4][4]

Head on over to Amazon for other reviews of [The Well-Grounded Java Developer, Vital techniques of Java 7 and polyglot programming][1]
  
The site for the book can be found here: http://www.manning.com/evans/ 

* * *Below is the complete table of contents so that you have a little more details about each chapter.</p> 

**Part 1 Developing with Java 7
  
Chapter 1 Introducing Java 7
  
** The language and the platform
  
Small is beautiful—Project Coin
  
The changes in Project Coin
  
Summary

**Chapter 2 New I/O**
  
Java I/O—a history
  
Path—a foundation of file-based I/O
  
Dealing with directories and directory trees
  
Filesystem I/O with NIO.2
  
Asynchronous I/O operations
  
Tidying up Socket-Channel functionality
  
Summary

**Part 2 Vital techniques
  
Chapter 3 Dependency Injection**
  
Inject some knowledge—understanding IoC and DI
  
Standardized DI in Java
  
Guice 3—the reference implementation for DI in Java
  
Summary

**Chapter 4 Modern concurrency**
  
Concurrency theory—a primer
  
Block-structured concurrency (pre-Java 5)
  
Building blocks for modern concurrent applications
  
Controlling execution
  
The fork/join framework
  
The Java Memory Model (JMM)
  
Summary

**Chapter 5 Class files and bytecode**
  
Classloading and class objects
  
Using method handles
  
Examining class files
  
Bytecode
  
Invokedynamic
  
Summary

**Chapter 6 Understanding performance tuning**
  
Performance terminology—some basic definitions
  
A pragmatic approach to performance analysis
  
What went wrong? Why we have to care
  
A question of time—from the hardware up
  
Garbage collection
  
JIT compilation with HotSpot
  
Summary

**Part 3 Polyglot programming on the JVM
  
Chapter 7 Alternative JVM languages
  
** Java too clumsy? Them’s fighting words!
  
Language zoology
  
Polyglot programming on the JVM
  
How to choose a non-Java language for your project
  
How the JVM supports alternative languages
  
Summary

**Chapter 8 Groovy: Java’s dynamic friend**
  
Getting started with Groovy
  
Groovy 101—syntax and semantics
  
Differences from Java—traps for new players
  
Groovy features not (yet) in Java
  
Interoperating between Groovy and Java
  
Summary

**Chapter 9 Scala: powerful and concise**
  
A quick tour of Scala
  
Is Scala right for my project?
  
Making code beautiful again with Scala
  
Scala’s object model—similar but different
  
Data structures and collections
  
Introduction to actors
  
Summary

**Chapter 10 Clojure: safer programming**
  
Introducing Clojure
  
Looking for Clojure—syntax and semantics
  
Working with functions and loops in Clojure
  
Introducing Clojure sequences
  
Interoperating between Clojure and Java
  
Concurrent Clojure
  
Summary

**Part 4 Crafting the polyglot project
  
Chapter 11 Test-driven development
  
** TDD in a nutshell
  
Test doubles
  
Introducing ScalaTest
  
Summary

**Chapter 12 Build and continuous integration**
  
Getting started with Maven 3
  
Maven 3—a quick-start project
  
Maven 3—the Java7developer build
  
Jenkins—serving your CI needs
  
Code metrics with Maven and Jenkins
  
Leiningen
  
Summary

**Chapter 13 Rapid web development**
  
The problem with Java-based web frameworks
  
Criteria in selecting a web framework
  
Getting started with Grails
  
Grails quick-start project
  
Further Grails exploration
  
Getting started with Compojure
  
A sample Compojure project—“Am I an Otter or Not?”
  
Summary

**Chapter 14 Staying well-grounded**
  
What to expect in Java 8
  
Polyglot programming
  
Future concurrency trends
  
New directions in the JVM

 [1]: http://www.amazon.com/gp/product/1617290068/ref=as_li_ss_tl?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=1617290068
 [2]: /wp-content/uploads/blogs/EnterpriseDev/Denis/evans_cover150.jpg?mtime=1357748948
 [3]: http://www.manning.com/evans/TWGJD_sample_ch01.pdf
 [4]: http://www.manning.com/evans/TWGJD_sample_ch04.pdf