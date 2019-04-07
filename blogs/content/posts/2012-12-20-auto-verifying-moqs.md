---
title: Auto-Verifying Moqs
author: Alex Ullrich
type: post
date: 2012-12-20T14:06:00+00:00
ID: 1873
excerpt: 'After years of only being familiar with Rhino mocks, I have been using Moq for the last 10 months or so.  For the most part, I like it better.  The syntax seems easier to get people up to speed on, and there are situations where it really cuts down on t&hellip;'
url: /index.php/enterprisedev/unittest/auto-verifying-moqs/
views:
  - 3919
rp4wp_auto_linked:
  - 1
categories:
  - Unit Testing
tags:
  - 'c#'
  - moq
  - tdd

---
After years of only being familiar with Rhino mocks, I have been using Moq for the last 10 months or so. For the most part, I like it better. The syntax seems easier to get people up to speed on, and there are situations where it really cuts down on the amount of test code you have to write. This is about one of the situations where it doesn&#8217;t.

One of the things I always liked about Rhino Mocks was the idea of the mock repository, and the fact that when disposing of a mock repository all of your setups get verified automagically. I have a lot of trouble remembering to add calls to VerifyAll when I&#8217;m adding functionality to existing tests because I got so used to this behavior in the past. When I noticed some tests where I had unnecessary mocks setup today I decided to do something about it. Its not really anything special but figued I&#8217;d share since I haven&#8217;t posted for a while.

Basically I added a base test fixture to the project that provides a means to create tracked mocks (similar to the MockRepository concept in Rhino). It provides a method for mock creation, and adds all created mocks to a list that is then verified in the teardown method. Pretty simple stuff but I found it handy.

```csharp
using System.Collections.Generic;
using Moq;
using NUnit.Framework;

namespace Project.Tests
{
    public abstract class MockVerifyingTest
    {
        readonly List<Mock> _trackedMocks = new List<Mock>();

        protected Mock<T> GenerateTrackedMock<T>(MockBehavior mockBehavior = MockBehavior.Default) where T : class
        {
            var mock = new Mock<T>(mockBehavior);
            _trackedMocks.Add(mock);
            return mock;
        }

        [TearDown]
        public virtual void TearDown()
        {
            try
            {
                foreach (var mock in _trackedMocks)
                {
                    mock.VerifyAll();
                }
            }
            finally
            {
                _trackedMocks.Clear();
            }
        }
    }
}
```

So now instead of something like this:

```csharp
[Test]
public void ATest() 
{
    var foo = new Mock<IFoo>(MockBehavior.Strict);

    foo.Setup(f => f.GetSomething()).Returns(new Something());

    var bar = new Bar(foo);

    bar.CodeUnderTest();

    foo.VerifyAll();
}
```

I can have my fixture inherit from MockVerifyingTest and write it like this:

< 

```csharp
[Test]
public void ATest() 
{
    var foo = GenerateTrackedMock<IFoo>(MockBehavior.Strict);

    foo.Setup(f => f.GetSomething()).Returns(new Something());

    var bar = new Bar(foo);

    bar.CodeUnderTest();
}
```

It only saves one line of test code in this example, but it can add up when dealing with tests that have several mocks. I realize having this many mocks in play for a test is just asking for trouble, but I am dealing with a legacy system without any test coverage, so working in test coverage without any sweeping refactorings is imperative at this point. We can make those changes later once we&#8217;ve gotten through a release or two with the code that is now under test 🙂