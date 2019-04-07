---
title: Installing Scala 2.10 on Eclipse Juno
author: SQLDenis
type: post
date: 2013-01-05T13:46:00+00:00
excerpt: |
  Scala 2.10 was released yesterday and I decided to take a look at it. But first what is Scala anyway? From the Scala site: 
  
  Scala is a general purpose programming language designed to express common programming patterns in a concise, elegant, and typ&hellip;
url: /index.php/enterprisedev/appserver/jee/installing-scala-2-10-on/
views:
  - 19898
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
As part of my [resolutions for the year][1] I said I would write more blog posts and also get into different technology. I decided to take a look at Scala. Well it turns out Scala 2.10 was released yesterday. But first what is Scala anyway? From the Scala site: 

> Scala is a general purpose programming language designed to express common programming patterns in a concise, elegant, and type-safe way. It smoothly integrates features of object-oriented and functional languages, enabling Java and other programmers to be more productive. Code sizes are typically reduced by a factor of two to three when compared to an equivalent Java application.

Scala is an object oriented, functional, statically typed language. You could probably compare it to F# if you are a .NET programmer.

The first thing you have to do is downloading version 2.10 of Scala, you can download that version here: http://www.scala-lang.org/downloads

After it is downloaded and installed, it is time to get the Eclipse plugin for Scala 2.10

The way you do this is you click on Help followed by Install New Software&#8230; from the menu in Eclipse

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno2.PNG?mtime=1357398056"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno2.PNG?mtime=1357398056" width="552" height="169" /></a>
</div>

Click on the Add button

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno3.PNG?mtime=1357398065"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno3.PNG?mtime=1357398065" width="476" height="177" /></a>
</div>

For Eclipse Juno you need to use the following URL in the location box http://download.scala-ide.org/sdk/e38/scala210/dev/site/
  
For Eclipse Indigo use the following URL http://download.scala-ide.org/sdk/e37/scala210/dev/site/
  
Give a name for the repository, I named mine Scala 10 for Eclipse Juno. Hit Ok

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno4.PNG?mtime=1357398076"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno4.PNG?mtime=1357398076" width="300" height="165" /></a>
</div>

Hit next

On the Install Details form you will see Scala IDE for Eclipse
  
You can expand it to reveal the following

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno5.PNG?mtime=1357398089"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno5.PNG?mtime=1357398089" width="352" height="152" /></a>
</div>

Hit next to accept the license, hit finish.

Now Eclipse will ask you to restart. After Eclipse is restarted it is time to create our first Scala application
  
From the menu go to File and then select New Project. Navigate to Scala Wizards and select Scala Project

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno6.PNG?mtime=1357398099"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno6.PNG?mtime=1357398099" width="562" height="361" /></a>
</div>

Now that the project is created let&#8217;s create a simple object

Right click on the Scala Project from the package Explorer and select New&#8211;> Scala Object

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno7.PNG?mtime=1357398110"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno7.PNG?mtime=1357398110" width="510" height="573" /></a>
</div>

Give it a name and check _public static void main_

You should have something like this

<pre>object Test2 {

  def main(args: Array[String]): Unit = {}

}</pre>

Let&#8217;s make it more interesting by generating some output

<pre>object Test2 {

  def main(args: Array[String]): Unit = {}

	val (name, site, role) = getSomeInfo()
	println("Name is " + name)
	println("Site is " + site)
	println("Role is " + role)

	def getSomeInfo() = {
    ("SQLDenis", "LessThanDot", "blogger")
	}
}</pre>

Run it by selecting Run As&#8211;> Scala Application from the Run as button

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno10.PNG?mtime=1357399576"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno10.PNG?mtime=1357399576" width="455" height="48" /></a>
</div>

Your output should be the following
  
Name is SQLDenis
  
Site is LessThanDot
  
Role is blogger

Why don&#8217;t we add a simple loop to our code? Here is what needs to be added

<pre>for (i <- 1 to 3) {
		print(i + ",")
	}
	println(" Testing 1,2,3.....")</pre>

Here is the whole code

<pre>object Test2 {

  def main(args: Array[String]): Unit = {}

	val (name, site, role) = getSomeInfo()
	println("Name is " + name)
	println("Site is " + site)
	println("Role is " + role)
	
	for (i <- 1 to 3) {
		print(i + ",")
	}
	println(" Testing 1,2,3.....")

	def getSomeInfo() = {
    ("SQLDenis", "LessThanDot", "blogger")
	}
}</pre>

Run it again, here is what the output should be

Name is SQLDenis
  
Site is LessThanDot
  
Role is blogger
  
1,2,3, Testing 1,2,3&#8230;..
  
Here is what my Eclipse window looks like

<div class="image_block">
  <a href="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno9.PNG?mtime=1357398314"><img alt="" src="/wp-content/uploads/blogs/EnterpriseDev/Denis/juno9.PNG?mtime=1357398314" width="595" height="523" /></a>
</div>

In case you are interested in all the new stuff in Scala 2.10, take a look at the stuff below.

* * *

The Scala 2.10.0 codebase includes the following new features and changes:&nbsp;

  * Value Classes 
      * A class may now extend `AnyVal` to make it behave like a struct type (restrictions apply).
      * <http://docs.scala-lang.org/overviews/core/value-classes.html>
  * Implicit Classes 
      * The implicit modifier now also applies to class definitions to reduce the boilerplate of implicit wrappers.
      * <http://docs.scala-lang.org/sips/pending/implicit-classes.html>
  * String Interpolation 
      * `val what = "awesome"; println(s"string interpolation is ${what.toUpperCase}!")`
      * <http://docs.scala-lang.org/overviews/core/string-interpolation.html>
  * Futures and Promises 
      * Asynchronously get some JSON: `for (req <- WS.url(restApiUrl).get()) yield (req.json  "users").as[List[User]]` (uses play!)
      * <http://docs.scala-lang.org/overviews/core/futures.html>
  * Dynamic and applyDynamic 
      * `x.foo` becomes `x.applyDynamic("foo")` if `x`&#8216;s type does not define a `foo`, but is a subtype of `Dynamic`
      * <http://docs.scala-lang.org/sips/pending/type-dynamic.html>
  * Dependent method types: 
      * `def identity(x: AnyRef): x.type = x` // the return type says we return exactly what we got
  * New ByteCode emitter based on ASM 
      * Can target JDK 1.5, 1.6 and 1.7
      * Emits 1.6 bytecode by default
      * Old 1.5 backend is deprecated
  * A new Pattern Matcher 
      * rewritten from scratch to generate more robust code (no more [exponential blow-up][2]!)
      * code generation and analyses are now independent (the latter can be turned off with `-Xno-patmat-analysis`)
  * Scaladoc Improvements 
      * Implicits (-implicits flag)
      * Diagrams (-diagrams flag, requires graphviz)
      * Groups (-groups)
  * Modularized Language features 
      * Get on top of the advanced Scala features used in your codebase by explicitly importing them.
      * <http://docs.scala-lang.org/sips/pending/modularizing-language-features.html>
  * Parallel Collections are now configurable with custom thread pools 
      * <http://docs.scala-lang.org/overviews/parallel-collections/overview.html>
  * Akka Actors now part of the distribution 
      * The original Scala actors are now deprecated.
      * See the [actors migration project][3] for more information.
  * Performance Improvements 
      * Faster inliner
      * \`Range#sum is now O(1)
      * Update of ForkJoin library
      * Fixes in immutable `TreeSet`/`TreeMap`
      * Improvements to PartialFunctions
  * Addition of `???` and `NotImplementedError`
  * Addition of `IsTraversableOnce` + `IsTraversableLike` type classes for extension methods
  * Deprecations and cleanup 
      * Floating point and octal literal syntax deprecation
      * Removed scala.dbc

### Experimental features {#Experimentalfeatures}

The following exciting &#8212; experimental &#8212; features are part of 2.10.0:

  * Scala Reflection 
      * <https://docs.google.com/document/d/1Z1VhhNPplbUpaZPIYdc0_EUv5RiGQ2X4oqp0i-vz1qw/edit#heading=h.pqwdkl>
  * Macros 
      * <http://docs.scala-lang.org/overviews/macros/overview.html>

* * *That is all for this post, Scala is just one of the languages I will explore in my quest to be more of a polyglot this year. Have you looked at Scala or some other functional language like F#?

Edit&#8230;&#8230;.

And I played a little more with this and decided to do one of our [Friday the Thirteenths][4]

Here is a solution that someone posted in Java

<pre>import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.GregorianCalendar;
 
public class Test
{
   private static final DateFormat format = new SimpleDateFormat("EEE MMM dd yyyy");
   
   public static void main(String... args) {
      GregorianCalendar cal = new GregorianCalendar();
      GregorianCalendar stopDate = new GregorianCalendar();
      stopDate.add(Calendar.YEAR, 10);
       
      // Move ahead to the next Friday
      while (cal.get(Calendar.DAY_OF_WEEK) != 6) cal.add(Calendar.DATE, 1);
       
      while (cal.before(stopDate)) {
         if (cal.get(Calendar.DAY_OF_MONTH) == 13)
            System.out.println(format.format(cal.getTime()));
           
            cal.add(Calendar.DATE, 7);
      }
   }
}</pre>

In Scala you don&#8217;t have to change that much, you can leave or take out the semicolons, here is the code

<pre>object Test2 {
  
import java.text.DateFormat
import java.text.SimpleDateFormat
import java.util.Calendar
import java.util.GregorianCalendar

  def main(args: Array[String]): Unit = {}
  val DateFormat  = new SimpleDateFormat("EEE MMM dd yyyy")
  
  val cal = new GregorianCalendar()
  val stopDate = new GregorianCalendar()
  stopDate.add(Calendar.YEAR, 10);
      
  while (cal.get(Calendar.DAY_OF_WEEK) != 6) cal.add(Calendar.DATE, 1)
	 
  while (cal.before(stopDate)) {
         if (cal.get(Calendar.DAY_OF_MONTH) == 13)
            println(DateFormat.format(cal.getTime()))
           cal.add(Calendar.DATE, 7);
      
   }
		
}</pre>

And here is the output

Fri Sep 13 2013
  
Fri Dec 13 2013
  
Fri Jun 13 2014
  
Fri Feb 13 2015
  
Fri Mar 13 2015
  
Fri Nov 13 2015
  
Fri May 13 2016
  
Fri Jan 13 2017
  
Fri Oct 13 2017
  
Fri Apr 13 2018
  
Fri Jul 13 2018
  
Fri Sep 13 2019
  
Fri Dec 13 2019
  
Fri Mar 13 2020
  
Fri Nov 13 2020
  
Fri Aug 13 2021
  
Fri May 13 2022

With SQL Server, you can just use a number table

<pre>SELECT DATEADD(m, number,'1998-01-13')
 FROM  master..spt_values WHERE type = 'P'
and DATENAME(dw,DATEADD(m, number,'1998-01-13')) = 'friday'</pre>

That is really it for this post&#8230;

 [1]: /index.php/ITProfessionals/ProfessionalDevelopment/ah-yes-those-pesky-resolutions
 [2]: https://issues.scala-lang.org/browse/SI-1133
 [3]: http://docs.scala-lang.org/actors-migration/
 [4]: http://forum.lessthandot.com/viewtopic.php?f=102&t=1608