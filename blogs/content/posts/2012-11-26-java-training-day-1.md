---
title: Java Training Day 1
author: SQLDenis
type: post
date: 2012-11-26T19:22:00+00:00
ID: 1802
excerpt: |
  Today is day one of the Java class I am taking this week. I am just dumping some of the stuff that I am hearing about, some of the stuff I might already know.
  Java is a general purpose language, it is interperted, output is by&hellip;
url: /index.php/enterprisedev/appserver/jee/java-training-day-1/
views:
  - 56119
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
  - Java EE
  - JBoss
  - Websphere
tags:
  - eclipse
  - java
  - maven
  - netbeans
  - oop

---
Java Training Day 1
  
&#8212;
  
Today is day one of the Java class I am taking this week. I am just dumping some of the stuff that I am hearing about, some of the stuff I might already know.
  
Java is a general purpose language, it is interperted, output is bytecode, you need to have a Java Virtual Machine on the target machine in order to run a Java program.
  
A Java file gets compiled to a system-neutral format which is a class file and gets a .class extension, this is compressed and not really human readable

Classes are organized in a group called a package, a package is very similar to a .net namespace. You use this so that you don't get collisions if you have two classes with the same name.

You use javadoc to generate standard formatted documentation.

**Java community process(JCP)**
  
Formal community-driven process to drive the Java standard, the iste is http://jcp.org

Deprecate, use annotation and then when you compile your class, you will get a warning so that you will know that you will need to rewrite that piece of the code.

Java is case sensitive, all reserved java keywords are lowercase

In a class the main routine is the start of a program
  
The main routine will look like this

public static void main(String[] args) {…….}

A java source file has to end in .java, for example MyClass.java this will produce a file called MyClass.class. you can't have more than one public class in a source file, you cannot have partial classes like in .net either.

Java runs on top of the JVM, the JVM is different per operating system. No need to recompile Java…Write once run everywhere….some people like to say write once…debug everywhere 🙂

**Java versions and flavors**
  
Java 1.0, 1.1
  
Java 2 1.2, 1.3, 1.4, 1.5
  
Java 5 also sometimes called 1.5, 1.5 is when they shifted to integers again
  
Java 6
  
Java 7

The different flavors of Java and their old and new names
  
J2SE Java SE
  
J2EE Java EE
  
J2ME Java ME

**Base libraries to provide common functionality**
  
Here are just some of them

java.lang
  
java.lang contains fundamental classes and interfaces closely tied to the language and runtime system.

java.io/java.nio/java.net
  
I/O and networking access the platform file system, and more generally networks through the java.io, java.nio and java.net packages. 

java.math
  
Mathematics package: java.math provides mathematical expressions and evaluation, as well as arbitrary-precision decimal and integer number datatypes.

java.text
  
Text: java.text deals with text, dates, numbers, and messages.

javax.crypto
  
Security is provided by java.security and encryption services are provided by javax.crypto.

java.sql
  
Databases: access to SQL databases via java.sql

java.beans
  
Java Beans: java.beans provides ways to manipulate reusable components.

**Types of Java Software**
  
Class Libraries
  
Standalone Java
  
Applets
  
Servlets, JSP and tag libraries
  
Enterprise Java Beans

**Enterprise Application Servers**
  
Here are a couple of Java Enterprise Application Servers and their vendors
  
GlassFish Oracle/Sun
  
Weblogic Oracle/BEA
  
Webshere IBM
  
JBoss Red Hat

**Environment variables**
  
Oh how I used to hate dealing with these variables, stuff wouldn't work if this wasn't setup correctly
  
There are a couple of environment variables that you need

**JAVA_HOME**
  
This will be use by the tools in the JDK

**PATH**
  
This is used by the operating system to find the compiler and other programs, add the/bin directory

**CLASSPATH**
  
This is used by the Java Virtual Machine to find application specific classes at compile as well as run time

**JDK Bin directory**
  
THe JDK Bin directory holds all the programs used by the JDK to do what is needed for Java to function
  
There are programs to compile java, to run java, to create JAR files, to create javadoc documentation files etc etc
  
Mine is installed here C:Program FilesJavajdk1.7.0_07bin

Here is screenshot of my directory with some of those programs

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/JavaBinDirectory.PNG?mtime=1353948732"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/JavaBinDirectory.PNG?mtime=1353948732" width="795" height="635" /></a>
</div>

There is something called jvisualvm.exe, this is know as Java VisualVM, from the Oracle documentation: http://docs.oracle.com/javase/6/docs/technotes/tools/share/jvisualvm.html
  
Java VisualVM is an intuitive graphical user interface that provides detailed information about Java technology-based applications (Java applications) while they are running on a given Java Virtual Machine (JVM*). The name Java VisualVM comes from the fact that Java VisualVM provides information about the JVM software visually.

Java VisualVM combines several monitoring, troubleshooting, and profiling utilities into a single tool. For example, most of the functionality offered by the standalone tools jmap, jinfo, jstat and jstack have been integrated into Java VisualVM. Other functionalities, such as some of those offered by the JConsole tool, can be added as optional plug-ins.

Here are some screenshots of the tool in action while running against Eclipse

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/VisualVMMonitor.PNG?mtime=1353948863"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/VisualVMMonitor.PNG?mtime=1353948863" width="929" height="579" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/VisualVMThreads.PNG?mtime=1353948850"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/VisualVMThreads.PNG?mtime=1353948850" width="919" height="581" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/VisualVMThreadsTable.PNG?mtime=1353948837"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/VisualVMThreadsTable.PNG?mtime=1353948837" width="930" height="594" /></a>
</div>

Always include . as part of the classpath, this ensure that the current directory is searched in case of a default package

**JAR**
  
Java Archive, this is an archived file holding all the classes and a manifest describing what is in the archive

There are related files that are used based on the type of application, here is what info wikipedia has listed

WAR (Web application archive) files, also Java archives, store XML files, Java classes, JavaServer Pages and other objects for Web Applications.
  
RAR (resource adapter archive) files (not to be confused with the RAR file format), also Java archives, store XML files, Java classes and other objects for J2EE Connector Architecture (JCA) applications.
  
EAR (enterprise archive) files provide composite Java archives which combine XML files, Java classes and other objects including JAR, WAR and RAR Java archive files for Enterprise Applications.
  
SAR (service archive) is similar to EAR. It provides a service.xml file and accompanying JAR files.
  
APK (Android Application Package), a variant of the Java archive format, is used for Android applications.[3]

We compiled and ran some programs from the command line, it is amazing how many people have problems with the path and classpath variables to get it to work…but we all did and now we are allowed to use Eclipese or Netbeans instead

**Javadoc comments**
  
We looked at how to do javadoc comments, basically the first line has to start with /**

There are also tags that you can use, it is recommended to use this order

@author (classes and interfaces only, required)
  
@version (classes and interfaces only, required. See footnote 1)
  
@param (methods and constructors only)
  
@return (methods only)
  
@exception (@throws is a synonym added in Javadoc 1.2)
  
@see
  
@since
  
@serial (or @serialField or @serialData)
  
@deprecated (see How and When To Deprecate APIs)

```java
Here is an example

   /**
     * Disposes of this graphics context once it is no longer 
     * referenced.
     *
     * @see       #dispose()
     * @since     1.0
     */
    public void finalize() {
        dispose();
    }
}
```

**Identifiers**
  
Can have letters and numbers, cannot begin with a digit

The reason most programming language don't allow identifiers to start with a digit is simple

int 6;
  
6=5;

See that? Since an identifier can be 1 character, you could create something named 5 or 6 or even 256, this rule prevents you doing such stuff

Variables can me made constant by using the final keyword for example

final int MAX_VALUE = 40;

You can also do this
  
final int BOILING_POINT;
  
BOILING_POINT; = 100;

Once you assign the value, you can't change BOILING_POINT anymore

We learned about scope and learned that depending on where the variable is declared, it might not be visible outside of the block, this is the same as in other languages (local variables)

When you want to have only one copy of a class variable, make it static, then if you instatiate 100 classes they all point to the same variable instead of having 100 variables in memory
  
Static variables are available to use as soon as a class is loaded

Brain fried……time to go…….. back tomorrow