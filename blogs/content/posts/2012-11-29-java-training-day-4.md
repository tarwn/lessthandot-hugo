---
title: Java Training Day 4
author: SQLDenis
type: post
date: 2012-11-29T18:55:00+00:00
ID: 1811
excerpt: |
  Java Day 4
  
  The Collections Framework
  Legacy Container Classes
  A container is an object that holds a collection of other objects
  An array is a simple container. Arrays have a couple of limitations
  . The size is fixed
  . All the members of the arra&hellip;
url: /index.php/enterprisedev/appserver/java-training-day-4/
views:
  - 17073
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
  - Application Server
  - Java EE
tags:
  - encapsulation
  - generics
  - inheritance
  - java
  - jvm
  - polymorphism

---
Today was day four of our Java training, we looked at generics, collection and IO today, here is what was covered

**The Collections Framework**
  
Legacy Container Classes
  
A container is an object that holds a collection of other objects
  
An array is a simple container. Arrays have a couple of limitations
  
. The size is fixed
  
. All the members of the array must be of the same type
  
. You can't add methods and attributes to an array

The java.util package has two container classes as well as an supporting interface

Vector class
  
Has add() and remove() methods, the elementAt() method let's you retrieve an element at a specified index
  
The Stack subclass provides the push() and pop() methods, this will support a LIFO stack

Hashtable class
  
This is an expandable associative array
  
The entries are stored as key/value pairs, no dups allowed
  
The Properties subclass will hold the name/value pairs

The Enumeration interface can be used to traverse either the vector or the hashtable objects. Enumeration has hasMoreElements() and nextElements() methods

Legacy Container Classes Disadvantages
  
It is not type safe, the containers can hold any kind of objects
  
The references must be downcast explicitly if you want to access the objects
  
They are synchronized, performance hit
  
Code needs to be rewritten if you want to replace one container with a container from another type

Vector, Hashtable and the Enumeration interface in Java SE 5+
  
Vector, Hashtable and the Enumeration interface have been redefined as generics
  
Vector is now Vector<E> and it implements the List<E>> interface
  
Hashtable is now Hashtable <K,V> and implements the Map<K ,V> interface
  
Enumeration is now Enumeration <E>.
  
Enumeration has been superseded by the Iterator interface

The enhanced for loop (or for each loop) works automatically with the generic versions of the containers

Collections Framework
  
The Collections Framework is a unified framework fo manipulating collections of objects
  
Programmers can choose from a variety of structures

The collections framework consists of:

  * Collection Interfaces – Represent different types of collections, such as sets, lists and maps. These interfaces form the basis of the framework.
  * General-purpose Implementations – Primary implementations of the collection interfaces.
  * Legacy Implementations – The collection classes from earlier releases, Vector and Hashtable, have been retrofitted to implement the collection interfaces.
  * Special-purpose Implementations – Implementations designed for use in special situations. These implementations display nonstandard performance characteristics, usage restrictions, or behavior.
  * Concurrent Implementations – Implementations designed for highly concurrent use.
  
    Wrapper Implementations – Add functionality, such as synchronization, to other implementations.
  * Convenience Implementations – High-performance "mini-implementations" of the collection interfaces.
  * Abstract Implementations – Partial implementations of the collection interfaces to facilitate custom implementations.
  * Algorithms – Static methods that perform useful functions on collections, such as sorting a list.
  * Infrastructure – Interfaces that provide essential support for the collection interfaces.
  * Array Utilities – Utility functions for arrays of primitives and reference objects. Not, strictly speaking, a part of the Collections Framework, this functionality was added to the Java platform at the same time and relies on some of the same infrastructure.

Collection Implementations
  
The general purpose implementations are summarized in the table below:

<div class="tables">
  <table border="0">
    <tr>
      <th colspan="2" rowspan="2" align="center" border="0">
        &nbsp;
      </th>
      
      <th colspan="5">
        <font size="+1">Implementations</font>
      </th>
    </tr>
    
    <tr>
      <th>
        Hash Table
      </th>
      
      <th>
        Resizable Array
      </th>
      
      <th>
        Balanced Tree
      </th>
      
      <th>
        Linked List
      </th>
      
      <th>
        Hash Table + Linked List
      </th>
    </tr>
    
    <tr>
      <th rowspan="4">
        <font size="+1">Interfaces</font>
      </th>
      
      <th>
        Set
      </th>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/HashSet.html"><tt>HashSet</tt></a>
      </td>
      
      <td>
        &nbsp;
      </td>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/TreeSet.html"><tt>TreeSet</tt></a>
      </td>
      
      <td>
        &nbsp;
      </td>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/LinkedHashSet.html"><tt>LinkedHashSet</tt></a>
      </td>
    </tr>
    
    <tr>
      <th>
        List
      </th>
      
      <td>
        &nbsp;
      </td>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/ArrayList.html"><tt>ArrayList</tt></a>
      </td>
      
      <td>
        &nbsp;
      </td>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/LinkedList.html"><tt>LinkedList</tt></a>
      </td>
      
      <td>
        &nbsp;
      </td>
    </tr>
    
    <tr>
      <th>
        Deque
      </th>
      
      <td>
        &nbsp;
      </td>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/ArrayDeque.html"><tt>ArrayDeque</tt></a>
      </td>
      
      <td>
        &nbsp;
      </td>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/LinkedList.html"><tt>LinkedList</tt></a>
      </td>
      
      <td>
        &nbsp;
      </td>
    </tr>
    
    <tr>
      <th>
        Map
      </th>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/HashMap.html"><tt>HashMap</tt></a>
      </td>
      
      <td>
        &nbsp;
      </td>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/TreeMap.html"><tt>TreeMap</tt></a>
      </td>
      
      <td>
        &nbsp;
      </td>
      
      <td>
        <a href= "http://docs.oracle.com/javase/6/docs/api/java/utilhttp://docs.oracle.com/javase/6/docs/api/java/util/LinkedHashMap.html"><tt>LinkedHashMap</tt></a>
      </td>
    </tr>
  </table>
</div>

Code to the interface, not to the class, you can now change the container without having to make code changes

```java
ArrayList<Integer>   list  = new ArrayList<Integer>();
LinkedList<Integer>  list =  new LinkedList<Integer>();
```
```java
List<Integer> list  = new ArrayList<Integer>();
List<Integer> list =  new LinkedList<Integer>();
```

There is a tutorial availabe on the Oracle website here: http://docs.oracle.com/javase/tutorial/collections/index.html

**Exceptions**

Traditional
  
Inefficient, checks must be done even if stuff doesn't blow up
  
Difficult to maintain
  
Using a return value as both an output value and an error status is confusing
  
Compiler does not enforce error checking

Using Exceptions
  
Compiler can enforce proper exception handling
  
Exception is caught by a block designed to handle the exception
  
Errors cause by constructors, initializers and other code that doesn't return a return value

All exceptions objects are subclasses of java.lang.Throwable and they inherit its methods
  
Error, Exception and RuntimeException are treated differently by the compiler
  
Error, this mean as major system-level error occured
  
RuntimeException , logic or data validateion error that should have been found and fixed during development
  
Exception, run-time problems like a file that can't be found or a network problem

A program can catch exceptions by using a combination of the try, catch, and finally blocks.

The try block identifies a block of code in which an exception can occur.
  
The catch block identifies a block of code, known as an exception handler, that can handle a particular type of exception.
  
The finally block identifies a block of code that is guaranteed to execute, and is the right place to close files, recover resources, and otherwise clean up after the code enclosed in the try block.

**Assert**
  
This was added in Java 1.4, it is a simple way to check run-time conditions and throw exceptions when you are debugging code
  
It can be enabled or disabled at runtime, by default they are disabled, use the -ea switch from the command line to enable it. Since Java doesn't have a preprocessor this would be a way to do testing without having to modify the code

**Input/Output**
  
We looked at IO and the different IO classes to work with files, streams sockets etc etc

Here is a list of the I/O Streams

Byte Streams
  
handle I/O of raw binary data.

Character Streams
  
handle I/O of character data, automatically handling translation to and from the local character set.

Buffered Streams
  
optimize input and output by reducing the number of calls to the native API.

Scanning and Formatting
  
allows a program to read and write formatted text.

I/O from the Command Line
  
describes the Standard Streams and the Console object.

Data Streams
  
handle binary I/O of primitive data type and String values.

Object Streams
  
handle binary I/O of objects.

That is all for today