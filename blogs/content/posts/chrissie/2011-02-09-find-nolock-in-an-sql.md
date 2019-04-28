---
title: Find NoLock in an sql statement and remove it.
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-09T09:32:00+00:00
ID: 1036
excerpt: 'My fellow blogger Ted Kreuger posted this powershell thing where he wants to remove the NOLOCk keyword from a statement (I have no idea why but he must have a good reason). So I commented that he could perhaps have done it with linq instead of regex. I&hellip;'
url: /index.php/desktopdev/mstech/find-nolock-in-an-sql/
views:
  - 4539
categories:
  - 'C#'
  - Microsoft Technologies

---
My fellow blogger Ted Kreuger posted [this powershell thing][1] where he wants to remove the NOLOCk keyword from a statement (I have no idea why but he must have a good reason). So I commented that he could perhaps have done it with linq instead of regex. I was mistaken linq has nothing to do with this. 

<span class="MT_red">Regex is evil.</span>

So I set up a testcase, I didn&#8217;t feel like doing it in but in csharp, just for fun.

First an interface.

```csharp
namespace NoLock
{
    public interface INoLock
    {
        string FindAndReplaceNoLock(string noLock);
    }
}```
Then some tests for the regex.

```csharp
using NUnit.Framework;

namespace NoLock
{
    /// &lt;summary&gt;
    /// TestNoLockLinq
    /// &lt;/summary&gt;
    /// &lt;remarks&gt; &lt;/remarks&gt;
    [TestFixture]
    public class TestNoLockRegex
    {
        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithInTheBeginning
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithInTheBeginning()
        {
            var nolock = new NoLockRegEx();
            Assert.AreEqual("SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name))",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department WITH (NOLOCK,INDEX(AK_Department_Name))"));
        }

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithAtTheEnd
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithAtTheEnd()
        {
            var nolock = new NoLockRegEx();
            Assert.AreEqual("SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name))",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name),NOLOCK)"));
        }

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithAlone
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithAlone()
        {
            var nolock = new NoLockRegEx();
            Assert.AreEqual("SELECT * FROM HumanResources.Department",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department WITH (NOLOCK)"));
        }

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsAtTheEndNoParenthesis
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsAtTheEndNoParenthesis()
        {
            var nolock = new NoLockRegEx();
            Assert.AreEqual("SELECT * FROM HumanResources.Department",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department NOLOCK"));
        }

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsAtTheEndWithParenthesis
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsAtTheEndWithParenthesis()
        {
            var nolock = new NoLockRegEx();
            Assert.AreEqual("SELECT * FROM HumanResources.Department",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department (NOLOCK)"));
        }

    }
}```
All red.

then Teds implementation.

```csharp
using System;
using System.Text.RegularExpressions;

namespace NoLock
{
    public class NoLockRegEx: INoLock
    {
        public string FindAndReplaceNoLock(string noLock)
        {
            if (Regex.IsMatch(noLock, "((\({0,1}NOLOCK\,)|(\,NOLOCK\({0,1}))", RegexOptions.IgnoreCase))
            {
                var replace = Regex.Replace(noLock, "((NOLOCK\,)|(\,NOLOCK\({0,1}))", "");
                return replace.Trim();
            }
            else if(Regex.IsMatch(noLock, "(WITH\s(\({0,1}NOLOCK\){0,1})|\({0,1}NOLOCK\){0,1})", RegexOptions.IgnoreCase))
            {
                var replace = Regex.Replace(noLock, "(WITH\s(\({0,1}NOLOCK\){0,1})|\({0,1}NOLOCK\){0,1})", "");
                return replace.Trim();
            }
            return "";
        }
    }
}```
All green.

Then tests for the alternativee version.

```csharp
using NUnit.Framework;

namespace NoLock
{
    /// &lt;summary&gt;
    /// TestNoLockLinq
    /// &lt;/summary&gt;
    /// &lt;remarks&gt; &lt;/remarks&gt;
    [TestFixture]
    public class TestNoLockAlt
    {

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithInTheBeginning
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithInTheBeginning()
        {
            var nolock = new NoLockAlt();
            Assert.AreEqual("SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name))",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department WITH (NOLOCK,INDEX(AK_Department_Name))"));
        }

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithAtTheEnd
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithAtTheEnd()
        {
            var nolock = new NoLockAlt();
            Assert.AreEqual("SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name))",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department WITH (INDEX(AK_Department_Name),NOLOCK)"));
        }

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithAlone
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsHidinginWithAlone()
        {
            var nolock = new NoLockAlt();
            Assert.AreEqual("SELECT * FROM HumanResources.Department",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department WITH (NOLOCK)"));
        }

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsAtTheEndNoParenthesis
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsAtTheEndNoParenthesis()
        {
            var nolock = new NoLockAlt();
            Assert.AreEqual("SELECT * FROM HumanResources.Department",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department NOLOCK"));
        }

        /// &lt;summary&gt;
        /// IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsAtTheEndWithParenthesis
        /// &lt;/summary&gt;
        [Test]
        public void IfFindAndReplaceNoLockReturnsWithoutNoLockWhenNoLockIsAtTheEndWithParenthesis()
        {
            var nolock = new NoLockAlt();
            Assert.AreEqual("SELECT * FROM HumanResources.Department",
                            nolock.FindAndReplaceNoLock(
                                "SELECT * FROM HumanResources.Department (NOLOCK)"));
        }

    }
}```
Hey, those look familiar.

All red.

And then the implementation.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace NoLock
{
    public class NoLockAlt: INoLock
    {
        public string FindAndReplaceNoLock(string noLock)
        {
            var noLockList2 = new List&lt;Tuple&lt;string,string&gt;&gt;() { new Tuple&lt;string,string&gt;("WITH (NOLOCK,","WITH ("), new Tuple&lt;string,string&gt;(",NOLOCK)",")")};
            foreach (var n in noLockList2.Where(n =&gt; noLock.Contains(n.Item1)))
            {
                return noLock.Replace(n.Item1, n.Item2).Trim();
            }
            var noLockList1 = new List&lt;String&gt; {"WITH NOLOCK", "WITH (NOLOCK)", "(NOLOCK)", "NOLOCK"};
            foreach (var n in noLockList1.Where(noLock.Contains))
            {
                return noLock.Replace(n, "").Trim();
            }
            return noLock;
        }
    }
}```
Yep these are the little things I do to keep myself busy.

 [1]: /index.php/DataMgmt/DBAdmin/powershell-replace-with-regex