---
title: Getting Flexible With NDepend 4 and CQLinq
author: Alex Ullrich
type: post
date: 2012-06-04T10:45:00+00:00
ID: 1641
excerpt: "At my last job we had a non-functional attribute that another team used to decorate service methods that they consumed.  The other team was working on an alternative client to our WCF services, and they weren't on the same release schedule they needed t&hellip;"
url: /index.php/architect/designingsoftware/getting-flexible-with-ndepend-4-and-cqlinq/
views:
  - 7081
rp4wp_auto_linked:
  - 1
categories:
  - Designing Software

---
At my last job we had a non-functional attribute that another team used to decorate service methods that they consumed. The other team was working on an alternative client to our WCF services, and they weren't on the same release schedule they needed to be able to target multiple versions of our services within a single version of their application. Because of this requirement, they maintained a wrapper around our services that handled some of the differences between versions. The main use for this attribute was to foster communication between the teams, so that if we changed a decorated method we would let them know. As I'm sure anyone on this other team would tell you, we weren't always that good about communicating these changes.

In an effort to make communication between teams easier we used a CQL query like this to report changes to these methods as part of our automated builds:

```SQL
SELECT METHODS FROM NAMESPACES "Services"
WHERE HasAttribute "OPTIONAL:Services.KnownExternalClientsAttribute"
AND CodeWasChanged
```

This was nice, but it only got us part of the way there. This would alert us to signature changes or changes to the content of the method, but not necessarily changes to the message contracts passed in to the method. In Pseudo-CQL the query I had in mind looks something like this:

```SQL
SELECT TYPES FROM NAMESPACES "Services"
WHERE CodeWasChanged
AND IsUsedBy (
    SELECT METHODS FROM NAMESPACES "Services"
    WHERE HasAttribute "OPTIONAL:Services.KnownExternalClientsAttribute"
)
```

This didn't work however (CQL doesn't really have support for subqueries), and I couldn't really find anything in the language that would allow us to achieve what we wanted. NDepend 4 introduces a new linq-based replacement called CQLinq that offers a lot more flexibility, so I figured I would see if I could write the query that we needed using it. It ended up being much easier than I thought – CQLinq gives us access to most (if not all) of the standard LINQ operators, and the same functions for querying code using attributes and history that we had with CQL. This is the query I came up with:

```CSHARP
// <Name>Test Query for Contract Changes</Name>
warnif count > 0

let decoratedMethods = from m in JustMyCode.Methods
    where m.HasAttribute("NDependSample.TestAttribute")
        && m.ParentNamespace.Name == "NDependSample.Services"
    select m

from t in JustMyCode.Types
where  t.ParentNamespace.Name == "NDependSample.Contracts"
  && t.CodeWasChanged()
  && decoratedMethods.Using(t).Any()
select t
```

Once we have the query we can mark it as critical, so we will have a failing build after the changes are made. Only the first build after making the changes should fail, but that would be enough to trigger an investigation that would result in communicating the changes to the other team.

CQL has always been my favorite feature of NDepend, so its no surprise that CQLinq is my favorite feature in this new release. The LINQ based syntax feels much more natural to me when writing queries against a codebase than the SQL-like syntax of CQL, and still gives us all the same visualization goodies to foster quick understanding of the query results. I'm really excited to dig in a little more and see what else I can do with it.