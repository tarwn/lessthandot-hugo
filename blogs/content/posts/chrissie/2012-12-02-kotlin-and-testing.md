---
title: Kotlin and testing
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-02T12:28:00+00:00
ID: 1818
excerpt: Learning how to write unittests in Kotlin.
url: /index.php/desktopdev/suntech/java/kotlin-and-testing/
views:
  - 7263
categories:
  - Java
tags:
  - kotlin
  - test

---
## Introduction

When learning a language it is important to know how to write unittests for it. Most modern languages have testing built in. And Kotlin is no exception. Kotlin has the kotlin.test namespace that has all the asserts you need. 

## Configuration

You will find plenty of examples how to write tests with Kotlin but something was escaping me because my tests would not run.

Let&#8217;s take our class.

```java
package demo

data class MyClass(val i : Int, val s : String)
```
uhm yeah.

Now lets try our first test.

```java
package demo.test

import demo.MyClass
import kotlin.test.assertEquals

public class tests
{
    test fun IfPropertyIIsSetCorrectly()
    {
        val c1 = MyClass(1,"t")
        assertEquals(1,c1.i)
    }
}```
I was excpeting the above to run. It doesn&#8217;t give a compilation error but the Intellij testrunner doesn&#8217;t like it. So I was doing something wrong.

The thing I missed was that it also needs JUnit. You can get maven to download and install it.

And add this line to you import.

```java
import org.junit.Test as test
```
Now the intellij testrunner will like them and run them.

## More tests.

And here are some more tests.

```
package demo.test

import demo.MyClass
import kotlin.test.assertEquals
import org.junit.Test as test
import kotlin.test.assertTrue
import kotlin.test.assertFalse

public class tests
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
}

```
But you can just as well do this.

```java
package demo.test

import demo.MyClass
import org.junit.Assert.assertEquals
import org.junit.Test as test
import org.junit.Assert.assertTrue
import org.junit.Assert.assertFalse

public class tests
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
}
```
Notice the difference?

## Conclusion

Not sure if kotlin.test adds much. We can just as well use Junit directly, you need it anyway.