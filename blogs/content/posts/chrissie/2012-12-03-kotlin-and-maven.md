---
title: Kotlin and Maven
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-03T09:54:00+00:00
ID: 1821
excerpt: Here is how I got my kotlin project to work with Maven.
url: /index.php/desktopdev/suntech/java/kotlin-and-maven/
views:
  - 7802
categories:
  - Java
tags:
  - kotlin
  - maven

---
## Introduction

Last week I asked if [Spek][1] was on a Maven repository so I could easily add it to my project and test it.
  
Someone who will remain anonymous (who still needs to wash my car) and runs the Spek project told me it wasn&#8217;t and that I could be so nice to add it.

I know next to nothing about Maven, except that it exists. So I went on the Google and found&#8230; a lot of things that I didn&#8217;t know about. But in the end [I found this][2]. 

And that helped just a little. 

## Setting up Maven

Apparently everybody knows how to set up Maven but forget to tell how it is done.
  
So first of all I downloaded Maven 3.0.4 and then unzipped that in the Program files folder (x86). Then I had to add this <code class="codespan">E:Program Files (x86)apache-maven-3.0.4bin</code> to my Path in the System Variables whiyou can find in Control Panel -> System -> Advanced settings -> Environment variables. I also needed to add the system variable JAVA_HOME and set it to this <code class="codespan">E:Program Files (x86)Javajdk1.7.0jre</code> or the path to your jre of your JDK. 

You can also add the system variable M2_HOME and set it to this <code class="codespan">E:Program Files (x86)apache-maven-3.0.4</code> or not. You can choose to add it manually to your module too.

If you get this error

> Error running kotlindeom [test]: No valid Maven installation found. Either set the home directory in the configuration dialog or set the M2_HOME environment variable on your system.

then the above did not work. In Intellij go to File -> Settings -> Maven and copy the Maven directory in the Maven home directory field.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven1.png?mtime=1354533891"><img alt="" src="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven1.png?mtime=1354533891" width="981" height="497" /></a>
</div>

And now Maven works with Intellij 12. Oh joy.

## MavenModule

It is now time to make our project.

Make a new project and make it a Maven module.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven2.png?mtime=1354534102"><img alt="" src="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven2.png?mtime=1354534102" width="594" height="640" /></a>
</div>

use the quickstart archetype, or any other archetype since we are not going to use it anyway.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven3.png?mtime=1354534163"><img alt="" src="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven3.png?mtime=1354534163" width="594" height="640" /></a>
</div>

override the maven home directory if you did not set M2_HOME or if you so desire.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven4.png?mtime=1354534303"><img alt="" src="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven4.png?mtime=1354534303" width="594" height="640" /></a>
</div>

Now we can change the pom.xml file to this.

```xml
&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;

    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

    &lt;groupId&gt;kotlindemos&lt;/groupId&gt;
    &lt;artifactId&gt;kotlindeom&lt;/artifactId&gt;
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;

    &lt;properties&gt;
        &lt;kotlin.version&gt;0.1-SNAPSHOT&lt;/kotlin.version&gt;
        &lt;junit.version&gt;4.11&lt;/junit.version&gt;
        &lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;
    &lt;/properties&gt;

    &lt;dependencies&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.jetbrains.kotlin&lt;/groupId&gt;
            &lt;artifactId&gt;kotlin-stdlib&lt;/artifactId&gt;
            &lt;version&gt;${kotlin.version}&lt;/version&gt;
        &lt;/dependency&gt;

        &lt;dependency&gt;
            &lt;groupId&gt;junit&lt;/groupId&gt;
            &lt;artifactId&gt;junit&lt;/artifactId&gt;
            &lt;version&gt;${junit.version}&lt;/version&gt;
            &lt;scope&gt;test&lt;/scope&gt;
        &lt;/dependency&gt;
    &lt;/dependencies&gt;

    &lt;build&gt;
        &lt;sourceDirectory&gt;${project.basedir}/src/main&lt;/sourceDirectory&gt;
        &lt;testSourceDirectory&gt;${project.basedir}/src/test&lt;/testSourceDirectory&gt;

        &lt;plugins&gt;
            &lt;plugin&gt;
                &lt;artifactId&gt;kotlin-maven-plugin&lt;/artifactId&gt;
                &lt;groupId&gt;org.jetbrains.kotlin&lt;/groupId&gt;
                &lt;version&gt;${kotlin.version}&lt;/version&gt;

                &lt;configuration/&gt;
                &lt;executions&gt;
                    &lt;execution&gt;
                        &lt;id&gt;compile&lt;/id&gt;
                        &lt;phase&gt;compile&lt;/phase&gt;
                        &lt;goals&gt;
                            &lt;goal&gt;compile&lt;/goal&gt;
                        &lt;/goals&gt;
                    &lt;/execution&gt;
                    &lt;execution&gt;
                        &lt;id&gt;test-compile&lt;/id&gt;
                        &lt;phase&gt;test-compile&lt;/phase&gt;
                        &lt;goals&gt;
                            &lt;goal&gt;test-compile&lt;/goal&gt;
                        &lt;/goals&gt;
                    &lt;/execution&gt;
                &lt;/executions&gt;
            &lt;/plugin&gt;
        &lt;/plugins&gt;
    &lt;/build&gt;

    &lt;repositories&gt;
        &lt;repository&gt;
            &lt;id&gt;jetbrains-all&lt;/id&gt;
            &lt;url&gt;http://repository.jetbrains.com/all&lt;/url&gt;
        &lt;/repository&gt;
    &lt;/repositories&gt;

    &lt;pluginRepositories&gt;
        &lt;pluginRepository&gt;
            &lt;id&gt;jetbrains-all&lt;/id&gt;
            &lt;url&gt;http://repository.jetbrains.com/all&lt;/url&gt;
        &lt;/pluginRepository&gt;
    &lt;/pluginRepositories&gt;
&lt;/project&gt;
```
If everything is well you should not see any red in there.

## Does it work?

Now we can start adding our kotlin files in.

Create a package kotlin.kotlindemo in the main and remove the package java. Add a kotlin file to that and use this code.

```java
package demo

data class MyClass(val i : Int, val s : String)```
Now add a kotlin.kotlindemo package to the test folder and add a kotlin file.

With this code.

```java
package demo.test

import demo.MyClass
import kotlin.test.assertEquals
import org.junit.Test as test
import kotlin.test.assertTrue
import kotlin.test.assertFalse

public class TestMyClass
{
    test fun IfPropertyIIsSetCorrectly()
    {
        val c1 = MyClass(1,"t")
        assertEquals(1,c1.i)
    }

    test fun IfPropertySIsSetCorrectly()
    {
        val c1 = MyClass(1,"t")
        assertEquals("t",c1.s)
    }

    test fun If2MyClassesWithTheSamePropertiesAreEqual()
    {
        val c1 = MyClass(1,"t")
        val c2 = MyClass(1,"t")
        assertTrue(c1.equals(c2))
    }

    test fun If2MyClassesWithDifferentPropertiesAreNotEqual()
    {
        val c1 = MyClass(1,"t")
        val c2 = MyClass(2,"t")
        assertFalse(c1.equals(c2))
    }

    test fun IfToStringReturnsTheCorrectString()
    {
        val c1 = MyClass(1, "t")
        assertEquals("MyClass(i=1, s=t)",c1.toString())
    }
}```
You might think this is the same class as in my previous blogpost but it isn&#8217;t. My previous class was called testMyClass and by convention Maven only finds Test\*, \*Test or TestCase* classes. I found that out the hard way. Lucky me.

Anyway, your project should look like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven5.png?mtime=1354535218"><img alt="" src="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven5.png?mtime=1354535218" width="387" height="348" /></a>
</div>

And now when we open our Maven project view. we should see this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven6.png?mtime=1354535357"><img alt="" src="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven6.png?mtime=1354535357" width="465" height="528" /></a>
</div>

you can now doubleclick on test and see the magic happen.

This should be the result.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven7.png?mtime=1354535495"><img alt="" src="/wp-content/uploads/users/chrissie1/kotlin/kotlinandmaven7.png?mtime=1354535495" width="742" height="313" /></a>
</div>

5 tests run and none faild. Woohoo.

## Conclusion

It took me a while to get it to work since I could not find Maven and IntelliJ for dummies and the Maven site is full of XML. But it works and I&#8217;m happy. Now on to Spek and getting the package into a repo, be afraid, be very afraid.

 [1]: https://github.com/hhariri/spek
 [2]: http://confluence.jetbrains.net/display/Kotlin/Kotlin+Build+Tools#KotlinBuildTools-Maven