---
title: 'When is a LambdaExpression Body a UnaryExpression in C#?'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-22T06:47:00+00:00
ID: 1303
excerpt: |
  A few weeks back I wrote about Fluentsecurty and the small bug it had which made it not work in VB.Net. Yesterday Kristoffer Ahl (The man behind the Fluentsecurity project) sent me a PM via twitter. 
  
  I've added your changes. Don't know how to test it properly though, without adding a VB.NET test project. Seems silly for 1 line! Ideas?
  
  And to be honest, I had no idea. But I did some research and found a way that might be good enough.
url: /index.php/desktopdev/mstech/when-is-a-lambdaexpression-a/
views:
  - 14316
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - fluentsecurity

---
## Introduction

A few weeks back I wrote about Fluentsecurty and [the small bug it had which made it not work in VB.Net][1]. Yesterday [Kristoffer Ahl][2] (The man behind the [Fluentsecurity][3] project) sent me a PM via twitter. 

> I&#8217;ve added your changes. Don&#8217;t know how to test it properly though, without adding a VB.NET test project. Seems silly for 1 line! Ideas? 

And to be honest, I had no idea. But I did some research and found a way that might be good enough.

## The code to test

The code to test is this.

```csharp
public static string GetActionName(this LambdaExpression actionExpression)
{
  return ((MethodCallExpression)actionExpression.Body).Method.Name;
}```
And the first thing to get was a failing test with this errormessage.

> System.InvalidCastException : Unable to cast object of type &#8216;System.Linq.Expressions.UnaryExpression&#8217; to type &#8216;System.Linq.Expressions.MethodCallExpression&#8217;.

Because that was the error I got from VB.Net.

## UnaryExpression

First thing to find out is, When is an expression a UnaryExpression.

You will find that [MSDN is not very helpful][4] here. It does show us how to create a UnaryExpression but we needed the Body of LambdaExpression to be a UnaryExpression so that did not help much.

So I have to find way to make sure I get a UnaryExpression from a LambdaExpression, while using a MethodCallExpression because that&#8217;s what this will be used for.

By accident I stumbled on [this StackOverflow question][5]. It did not have a solution to my problem but it did have the problem I was trying to recreate.

And this was the most interesting part.

> What causes some of the lambdas in my example to yield MemberExpression values and others UnaryExpression values? In my example, the first UnaryExpression is on the third line, the DateTime property, but boolean properties also result in UnaryExpressions.

.

So the UnaryExperssion is caused by boxing and we all know how we can cause that, right? If not, there is plenty to read about it on [MSDN][6].

And here is now my failing test.

```csharp
[TestFixture]
    class TestClass1
    {
        class Test1
        {
            public Boolean test()
            {
                return false;
            }
        }

        [Test]
        public void TestIfGetActionNameDoesNotThrowErrorWhenPassingUnaryExpression()
        {
            Expression&lt;Func&lt;Test1, object&gt;&gt; expression = x =&gt; x.test();
            expression.GetActionName();
        }
    }```
We can now change the code in our method to the one I already had in the previous post.

```csharp
public static string GetActionName2(this LambdaExpression actionExpression)
{
  var expression = (MethodCallExpression)(actionExpression.Body is UnaryExpression ? ((UnaryExpression)actionExpression.Body).Operand : actionExpression.Body);
  return expression.Method.Name;
}```
And we now see out test pass.

## Conclusion

Making a LambdaExpression into a convincing to be a UnaryEpression is easy, once you know how ;-).

 [1]: /index.php/WebDev/ServerProgramming/ASPNET/fluent-security-and-making-it
 [2]: https://twitter.com/#!/kristofferahl
 [3]: http://www.fluentsecurity.net/
 [4]: http://msdn.microsoft.com/en-us/library/system.linq.expressions.unaryexpression.aspx
 [5]: http://stackoverflow.com/questions/3567857/why-are-some-object-properties-unaryexpression-and-others-memberexpression
 [6]: http://msdn.microsoft.com/en-us/library/yz2be5wk.aspx