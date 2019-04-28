---
title: Linq to nHibernate
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-13T08:33:38+00:00
ID: 756
url: /index.php/desktopdev/mstech/linq-to-nhibernate/
views:
  - 4075
categories:
  - Microsoft Technologies
tags:
  - linq
  - nhibernate
  - vb.net

---
Today I thought it would be good to try some Linq to nHibernate while I was waiting for VS2010 to finish downloading.

First thing I had to do was download [NHibernate 2.1.2 GA][1] and [the Linq provider][2] that goes with it.

I was using 2.0.1 up until now. So I had to add a [Bytecode provider to my configuration][3] routine. That wasn&#8217;t too difficult (I thought).

This was the line I used.

```vbnet
_Configuration.SetProperty(Environment.ProxyFactoryFactoryClass, "NHibernate.ByteCode.LinFu.ProxyFactoryFactory, NHibernate.ByteCode.LinFu")
            ```
And&#8230; it didn&#8217;t work. Strange. I was sure I added the references to the Linfu provider but still it gave me this error.

> Unable to load type &#8216;NHibernate.ByteCode.LinFu.ProxyFactoryFactory, NHibernate.ByteCode.LinFu&#8217; during configuration of proxy factory class.
  
> Possible causes are:
  
> &#8211; The NHibernate.Bytecode provider assembly was not deployed.
  
> &#8211; The typeName used to initialize the &#8216;proxyfactory.factory_class&#8217; property of the session-factory section is not well formed.
> 
> Solution:
  
> Confirm that your deployment folder contains one of the following assemblies:
  
> NHibernate.ByteCode.LinFu.dll
  
> NHibernate.ByteCode.Castle.dll

Well, apparently setting a reference doesn&#8217;t mean the assembly gets copied over even if you say copy local = true. You have to actually use the assembly to make it copy. Or include it as a file, but I don&#8217;t like that idea. 

Adding this somewhere:

```vbnet
Dim i = New NHibernate.Bytecode.LinFu.ProxyFactoryFactory```
will help the process along.

And did you see that VB.Net makes it Bytecode while the assembly uses ByteCode? Groovy.

Now we got that out of the way. 

Then I tried to change this little query.

```vb.net
_Session = SessionFactory.OpenSession
_Query = _Session.CreateQuery("FROM TexCase as e Where e.CaseStatus=:Open")
              _Query.SetEntity("Open",_CaseStatus.FindCaseStatusByCaseStatus("Open") )
_TexCases = _Query.List(Of TexCase)()
```
It&#8217;s a very simple query but the magic string bothers me. 

In Linq it looks like this.

```vbnet
_Session = SessionFactory.OpenSession
Dim _Query = From e In _Session.Linq(Of TexCase)() Where e.CaseStatus.Equals(_CaseStatus.FindCaseStatusByCaseStatus("Open")) Select e
_TexCases = _Query.ToList```
Not really much shorter but a lot more friendly when it comes to refactoring. And the result is exactly the same. I like it so far.

 [1]: http://sourceforge.net/projects/nhibernate/files/NHibernate/2.1.2GA/NHibernate-2.1.2.GA-bin.zip/download
 [2]: http://sourceforge.net/projects/nhibernate/files/NHibernate/2.1.2GA/NHibernate.Linq-2.1.2-GA-Bin.zip/download
 [3]: http://nhforge.org/blogs/nhibernate/archive/2008/11/09/nh2-1-0-bytecode-providers.aspx