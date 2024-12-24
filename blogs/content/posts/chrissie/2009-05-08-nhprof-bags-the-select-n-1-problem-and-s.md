---
title: NHProf, bags, the select N+1 problem and solving it
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-08T05:49:02+00:00
ID: 415
url: /index.php/desktopdev/mstech/nhprof-bags-the-select-n-1-problem-and-s/
views:
  - 7688
categories:
  - Microsoft Technologies
tags:
  - .net
  - nhibernate
  - nhprof

---
Ok so here is how NHProf has helped me solve a problem. This one in particular is a [Select N+1][1] problem which means is that you are executing the same select several times in one session which usualy means you are doing something wrong. 

First up the code.

When doing a find all on a class called Fibergroup I get this as a result from [NHProf][2].

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/NHProf/NHProf1.png" alt="" title="" width="744" height="313" />
</div>

What we see is that there is one select tbl_fibergroup and then then a select on fiberclass and several selects on fibersubclass. As a matter of fact fiberclass is a collection in fibergroup and fibersubclass is a collection in fiberclass.

Now a normal human being would do this in one sql since all the tables are connected and nhibernate should know that. Now what is wrong.

Well by default Nhibernate sets it&#8217;s fetching strategie to select which means it will create a select for each object it needs to get.
  
[
  
A quote from the nHibernate documentation.][3]

> Select fetching &#8211; a second SELECT is used to retrieve the associated entity or collection. Unless you explicitly disable lazy fetching by specifying lazy=&#8221;false&#8221;, this second select will only be executed when you actually access the association. 

This is done for lazyloading which is the default. I don&#8217;t use lazy loading. Select will also default to a batchsize of one as it seems. 

Setting the batchsize to a higher number will solve the problem and greatly reduce the number of selects.

this was the mapping attribute I used on the Fiberclasses property in the fibergroup class

```xml
&lt;Bag(0, Generic:=True, Cascade:="save-update", lazy:=False), _
            Key(1, Column:="FiberGroup_id"), _
            OneToMany(2, ClassType:=GetType(FiberClass))&gt; _
        ```
this translate to this XML

```xml
&lt;bag name="FiberClasses" lazy="false" cascade="save-update" generic="true"&gt;
      &lt;key column="FiberGroup_id" /&gt;
      &lt;one-to-many class="TDB2007.Dal.Model.AnalysisManagement.FiberClass, TDB2007.Dal.Model" /&gt;
    &lt;/bag&gt;```
and this is the new one.

```xml
&lt;Bag(0, Generic:=True, Cascade:="save-update", lazy:=False, Fetch:=CollectionFetchMode.Select, BatchSize:=10), _
            Key(1, Column:="FiberGroup_id"), _
            OneToMany(2, ClassType:=GetType(FiberClass))&gt; _```
And to this XML

```xml
&lt;bag name="FiberClasses" lazy="false" fetch="select" cascade="save-update" batch-size="10" generic="true"&gt;
      &lt;key column="FiberGroup_id" /&gt;
      &lt;one-to-many class="TDB2007.Dal.Model.AnalysisManagement.FiberClass, TDB2007.Dal.Model" /&gt;
    &lt;/bag&gt;```
After that change, I go down from 30 selects to 7 selects. Quite an improvement I think.

And here is the proof.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/NHProf/NHProf3.png" alt="" title="" width="700" height="120" />
</div>

BTW I tried to use the fetch strategy &#8220;join&#8221; but that gave me the wrong results. From what I have read join doesn&#8217;t work so well on bag only on set, but I could be wrong.

 [1]: http://nhprof.com/Learn/Alert?name=SelectNPlusOne
 [2]: http://nhprof.com/
 [3]: https://www.hibernate.org/hib_docs/nhibernate/1.2/reference/en/html/performance.html