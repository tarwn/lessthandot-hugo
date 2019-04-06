---
title: Fun With NDepend
author: Alex Ullrich
type: post
date: 2008-12-22T12:24:12+00:00
url: /index.php/desktopdev/mstech/fun-with-ndepend/
views:
  - 6114
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - .net
  - analysis
  - 'c#'
  - ndepend

---
I just finished another course at school that involved studying an open-source application. I&#8217;ve gotten tired of studying Java and using Java tools, so I decided to try studying a .net project. Luckily I stumbled upon [NDepend][1] which is the most fully featured tool for studying .net code that I&#8217;ve seen to date.

In the past when I needed to do some kind of code analysis (usually generating DSM&#8217;s and maybe taking a brief look at some metrics), Lutz Roeder&#8217;s (I mean red-gate&#8217;s) [Reflector][2] and its&#8217; various [add-ins][3] has been sufficient for me, and not to mention free. But I found these tools to be somewhat limiting when doing large amounts of analysis (the kind I don&#8217;t typically get time to do at work!)

I&#8217;ve really enjoyed working on these code analysis projects in the past, but this was my favorite one yet. It was more enjoyable to me because I was working on a .net project, making it easier to see how I can directly apply this to my own work (while working on the Java projects does a good job of teaching the concepts involved, it is hard to apply them to my work without the tools). But it was also enjoyable because NDepend was such a joy to use (and I think I&#8217;ve just barely scratched the surface of what it can do).

NDepend can do all the things you&#8217;d expect from a code analysis tool. It does a good job creating DSM&#8217;s, has very pretty visual displays of said DSM&#8217;s, and provides quick access to code metrics. I especially like the mouse-over highlighting in the visual dependency graphs, which helps you to tell at a moments notice what depends on a class, what the class depends on, whether any cyclic dependencies are present, and a ballpark idea of how many dependencies exist. I haven&#8217;t gotten into any of the .net or visual-studio specific features yet (but I am sure they will make life much easier).

My favorite feature (by far) in working on this project was its&#8217; &#8220;Code Query Language&#8221; (CQL). CQL is a SQL-like dialect that lets you write queries directly against your codebase. This allows you to get very quick answers to questions like &#8220;What classes in my assembly depend on FooClass?&#8221; or &#8220;How many classes have more than <insert arbitrary threshold here> Efferent Couplings?&#8221;. So if you need to find out some information about your code, instead of compiling metrics and poring over the results, you only need to ask NDepend the question directly, and see the answer as fast as you can type. After using this feature for a month or two, I don&#8217;t know how I could go back to living without it!

An example query would look something like this:

<pre>// &lt;Name&gt;Most Complex Methods used by Type X&lt;/Name&gt;
SELECT TOP 10 METHODS 
WHERE IsUsedBy "AssemblyX.NamespaceY.TypeZ" 
ORDER BY CyclomaticComplexity DESC</pre>

In a larger project, you can use the FROM clause to limit what you bring back to methods from a particular assembly or namespace, like so:

<pre>// &lt;Name&gt;Most Complex Methods from Assembly Q used by Type X&lt;/Name&gt;
SELECT TOP 10 METHODS 
FROM ASSEMBLIES "AssemblyQ"
WHERE IsUsedBy "AssemblyX.NamespaceY.TypeZ" 
ORDER BY CyclomaticComplexity DESC</pre>

Another cool thing about CQL is the ability to add constraints. So you could set it up to be warned whenever you run analysis if you had a method with over 200 lines of code, or methods with excessive complexity and an insufficient number of comments. Here is an example of a constraint that ships with NDepend, meant to warn the user if any fields break encapsulation.

<pre>// &lt;Name&gt;Fields should be declared as private&lt;/Name&gt;
WARN IF Count &gt; 0 IN SELECT TOP 10 FIELDS WHERE 
 !IsPrivate AND 
 // These conditions filter cases where fields doesn't represent state that should be encapsulated. 
 !IsInFrameworkAssembly AND 
 !IsGeneratedByCompiler AND 
 !IsSpecialName AND 
 !IsInitOnly AND 
 !IsLiteral AND 
 !IsEnumValue</pre>

As I think these few examples show, this CQL is very intuitive, and I really recommend checking it out if you get a chance. Even being quite the SQL junkie myself, I found it hard to find anything to complain about. I especially liked the visual display of the query results. Basically in the NDepend window there is a rectangle filled with unevenly sized blocks. Each block represents a field or method (depending what you are looking at) and they form larger blocks representing types. The blocks can be sized based on different attributes like number of lines of code, or number of efferent couplings. Below is a screenshot (from NDepend&#8217;s website) showing what this looks like:

![screenshot][4]

When all is said and done, I&#8217;ve really come to love, and even depend on NDepend. I think it will be an indisposable part of my toolkit. What is scary about this is that I haven&#8217;t even gotten started yet. I haven&#8217;t used the build comparison feature that lets you watch how your assembly evolves over time (in the past, when I&#8217;ve used the Java tools, I needed to create a separate project for each, version, and compare manually, so I&#8217;m sure I will love this feature!). Another thing that I think is really cool is the fact that you can integrate the NDepend analysis with your build process, so you can be warned of violations of your established constraints in real time, not just whenever you get around to running another analysis. You can even embed the constraints in your source code! When I get into all these features I&#8217;ll be sure to share.

\*** If you have questions on C#, check out our [C# Forum][5] or even the [ASP.net Forum][6]

 [1]: http://ndepend.com/
 [2]: http://www.red-gate.com/products/reflector/index.htm
 [3]: http://www.codeplex.com/reflectoraddins
 [4]: http://www.ndepend.com/Res/NDependBig08.PNG "CQL Window Screenshot"
 [5]: http://forum.lessthandot.com/viewforum.php?f=40
 [6]: http://forum.lessthandot.com/viewforum.php?f=27