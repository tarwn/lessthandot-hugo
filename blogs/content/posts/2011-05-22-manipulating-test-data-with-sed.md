---
title: Manipulating Test Data with Sed
author: Rob Earl
type: post
date: 2011-05-22T12:41:00+00:00
excerpt: "Following on from my previous blog about sed, this blog will detail how I use sed most often: manipulating test data when running demonstrations of new features. Let's say we've developed a web service which creates users from the following XML document&hellip;"
url: /index.php/sysadmins/os/linux/manipulating-test-data-with-sed/
views:
  - 2468
rp4wp_auto_linked:
  - 1
categories:
  - Linux

---
Following on from my [previous blog about sed][1], this blog will detail how I use sed most often: manipulating test data when running demonstrations of new features. Let&#8217;s say we&#8217;ve developed a web service which creates users from the following XML document:

<pre>&lt;user&gt;
    &lt;username&gt;USERNAME&lt;/username&gt;
    &lt;password&gt;PASSWORD&lt;/password&gt;
&lt;/user&gt;</pre>

We might want to demonstrate this web service using a number of combinations of usernames and passwords, for instance:

  * robearl/password
  * rob\_earl/pass\_word
  * rob&#8217;earl/pass&#8217;word
  * rob-earl/pass-word
  * etc.

To do these tests we might create four XML documents containing each username/password pair. Every different combination we wanted to demonstrate would require a new document.

Instead, we can use sed. Create a single document like the one above and pass it through sed to replace the variables before passing it to the web service using curl:

<pre>cat single_document.xml | sed -e 's/USERNAME/robearl/' -e 's/PASSWORD/password/' | curl &lt;options&gt; -d @- &lt;web service&gt;</pre>

Using this technique, we avoid having to create lots of XML documents and it&#8217;s obvious what data is being passed without having to open up each document during the demonstration.

 [1]: /index.php/SysAdmins/OS/Linux/bulk-editing-with-sed