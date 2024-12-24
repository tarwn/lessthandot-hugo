---
title: CQL From Visual Studio With NDepend 3
author: Alex Ullrich
type: post
date: 2010-09-06T20:29:00+00:00
ID: 894
excerpt: "For the last few months I've had the pleasure of working with NDepend version 3.  Most of my development at home is on linux these days, so I haven't used it as much as I'd like, but I have been using it to poke around in various codebases and see what&hellip;"
url: /index.php/architect/designingsoftware/cql-from-visual-studio-with-ndepend-3/
views:
  - 19026
rp4wp_auto_linked:
  - 1
categories:
  - Designing Software
tags:
  - .net
  - analysis
  - 'c#'
  - ndepend

---
For the last few months I've had the pleasure of working with [NDepend][1] version 3. Most of my development at home is on linux these days, so I haven't used it as much as I'd like, but I have been using it to poke around in various codebases and see what the new Visual Studio integration is all about. The last version integrated with Visual Studio, technically speaking, but it didn't seem nearly as thorough as what I've seen in version 3. I suspect the improved extensibility model in VS 2010 has a lot to do with this, but can't confirm (I haven't tried it with 2008 either).

My favorite feature of NDepend has always been CQL, the SQL-like query language that allows you to query your codebase using a variety of common metrics. This is the same as it ever was (the integration with VS is even quite similar) but with the more thorough integration it seems much more useful. I like how easy it is now to keep an eye on my CQL constraints when I rebuild.

My favorite CQL feature is the ability to set up CQL constraints _from now_. This is really cool for older projects, where it's unrealistic to think that your team will be able to fix everything right away. But what you can do with this feature is ensure that all _new or modified_ code does measure up to your team's standards. You may not be able to clean up all those 1,000 line methods right away, but you _can_ ensure that newer methods fit in a more reasonable size limit (like 975 😉 ). This is one of the most useful features of the application, IMO. The way it works is by allowing you to establish a baseline. By comparing the code's current state to this baseline, future analyses are able to determine which methods/types/etc are new or changed, and apply the constraint to only these methods/types/etc. Sometimes I feel like this would be useful just to be able to concisely see which methods have been changed as well (version control logs aren't the most friendly things to read, especially spread throughout a large codebase). Below is a screenshot of this feature in action.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/Architect/CQL-VS-NDepend3/cqlexplorer.PNG" alt="CQL Explorer" title="CQL Explorer Screenshot" width="971" height="283" />
</div>

The first three queries listed are built in to NDepend. I added the fourth, just to have a listing of new/changed methods ready. The CQL for this query is simply 

```text
SELECT METHODS WHERE WasAdded OR CodeWasChanged
```

Not a bad way to keep an eye on what is getting changed in the codebase. To get in this state I added three new methods to the codebase I was looking at (in a place that I could remove them easily since they are not only low quality but useless as well). Two had 7 parameters, putting them in violation of the constraint for basic quality principles. I didn't add any tests, so all three were in violation of the test coverage constraint. And finally they all showed up in the list of new methods. It's worth noting the yellow circle at the bottom right as well – the yellow means that warnings were encountered when running the CQL portion of the analysis. Green would be good, and red would mean I have some bad queries that can't be run. 

Double clicking a row in the CQL Explorer will take you to the CQL Editor – from here you can view the results of the query, and the CQL it contains. From there you can easily navigate to the method definition in your source code by double-clicking. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/Architect/CQL-VS-NDepend3/cqleditor.PNG" alt="CQL Editor" title="CQL Editor Screenshot" width="457" height="913" />
</div>

One of the things I really like here is the comments in the built-in queries. They contain numerous links to metric definitions on the NDepend website, and sometimes even links to blog entries where the lead developer, [Patrick Smacchia][2] has explained features in greater detail. I really like this form of documentation, it makes it easier to keep up to date and also minimizes what needs to be stored on the user's computer. 

What I was happiest to find in the VS integration is the ability to superimpose CQL results onto the metrics view. The metrics view consists of a grid where each block represents a unit of code (type, method, etc...) and they are sized according to their value for the metric in question. When running CQL queries, the units of code matching your criteria are highlighted, giving a great visualization of how much code exhibits the properties you are looking for.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/Architect/CQL-VS-NDepend3/metricsexplorer.PNG" alt="Metrics Explorer" title="CQL Metrics Explorer" width="887" height="686" />
</div>

The selected query (about types having too many efferent couplings) is the currently selected query, and I had moused over the QueryParser class to highlight it in pink and show the metrics summary on the right. I find that having this built right into visual studio really helps me figure out where to focus my refactoring energy.

It looks as if a lot of effort went into [making the CQL validation phase fast][3]:
  


> CQL rules validation phase is fast. The performance challenge was to make this happens almost instantly to avoid slowing the developer machine. Hopefully for a large 100K Lines of Code application, code gets re-analyzed and 200 CQL rules can get checked, all within 3 seconds after the (re)compilation of one or several .NET assemblies. These fast performances were made possible thanks to the development of a new technology of incremental code analysis. With incremental code analysis, only modified code gets re-analyzed. I can attest that this was extremely challenging and complex development!</p>
From what I've seen, the effort's been a success. I've mostly been using it to look at different versions of [Lucene.net][4], as I've got some work to do to get some of my code to build in VS2010. For this size codebase (~23k lines) the analysis is completed very quickly, even if I disable the incremental analysis. The CQL validation portion completes almost instantly, and memory usage doesn't seem to get out of hand even when keeping VS open for days. I'd imagine if your computer can handle running Visual Studio to begin with, it won't have too much trouble with the NDepend integration. I could see some of the VS add-ins that don't play well with others causing issues, especially with a very large codebase, so I hope to go back and test with a larger codebase and some other add-ins installed eventually.

Most of the other NDepend goodies are available in VS now as well (Dependency Graphs, Test Coverage Analysis, Class Browser, etc...) but I won't get into all that here. I really see CQL as the app's killer feature, and that is what I spend the most time thinking about. There is a good overview of the app's capabilities [here][5] if you'd like to read more.

 [1]: http://ndepend.com/
 [2]: http://codebetter.com/blogs/patricksmacchia/default.aspx
 [3]: http://codebetter.com/blogs/patricksmacchia/archive/2010/08/29/validating-code-rules-in-visual-studio.aspx
 [4]: http://lucene.apache.org/lucene.net/
 [5]: http://www.ndepend.com/Features.aspx#Tour