---
title: Simple advice for choosing an ESB
author: damber
type: post
date: 2009-04-06T18:54:28+00:00
ID: 374
excerpt: |
  I thought I would share a recent question that was raised on LinkedIn, which asked:
  
  
  Starting the Holy Wars on ESB? Who brings the best package deal to the table i.e IBM, MSFT...... Look at this way " A mid sized cash crunched has to implement ESB w&hellip;
url: /index.php/architect/enterprisearchitecture/simple-advice-for-choosing-an-esb/
views:
  - 11555
rp4wp_auto_linked:
  - 1
categories:
  - Enterprise Architecture
  - Information and Integration Architecture
tags:
  - esb
  - ibm
  - integration
  - open source
  - oracle fusion
  - sap xi
  - webmethods

---
I thought I would share a recent question that was raised on LinkedIn, which asked:

> _**Starting the Holy Wars on ESB? Who brings the best package deal to the table i.e IBM, MSFT…… Look at this way ” A mid sized cash crunched has to implement ESB what are the options it has” or rather who should it be married to. Any thoughts**_
> 
> Tons of ESB from various players are in the market IBM, MSFT, Oracle , SAP but remember its a cash crunched company which requires the ESB. I can provide more details on the technical side. The idea is to throw open the debate to get various perspective. 

Well, let's forget for the moment that the question was raised by a Microsoft employee, and focus more on the general principle of the question… What should companies look for in an [ESB][1]? In my brief answer, I aim to get across the point that asking which ESB vendor you should be “Married to” is very much the wrong question to ask, and to consider that there is more to solving this problem intelligently (and thus cost effectively) than simply &#8216;buying technology'. No single technology will solve your problem out of the box, so be careful not to expect or ask it to do so. 

Here's the Answer in full:

> As always… understand the problem first…
> 
> Step 1: Understand the problem you are trying to solve (e.g. Imperatives, Current Pain Points, As-Is Landscape, Use Cases, etc)
  
> Step 2: Design a Target Architecture (NOT based on a particular technology) that solves this problem, including reference models, design patterns, architecture paradigms, principles, etc
  
> Step 3: Create a high level Roadmap to get from your current environment to your target architecture
  
> Step 4: Now… Search for a technology that will support this need.
> 
> I would highly recommend not starting by talking to vendors about how they will solve your problem. Strangely enough, their products always have the answer 🙂
> 
> ok.. let's look at what integration is all about:
> 
> Openness
  
> Interoperability
  
> Standardisation
  
> Flexibility
> 
> &#8211; This is where you need to really think about why you have an integration problem in the first place… closed, insular approaches to application design. This indicates that we want an OPEN and INTEROPERABLE environment which is based on STANDARDS to give you FLEXIBILITY.
> 
> The problem with a lot of products on the market if not understood or used properly is that you can very soon become (in your words) “Married” to that vendor's solution. This destroys the core principles of what integration is all about &#8211; you are simply adding the same problem on top of the original problems.
> 
> In many ways the open source integration solutions (incl. ESBs &#8211; e.g. SOPERA (commercial and more advanced version of Eclipse Swordfish), Apache FUSE, JBoss,etc) lend themselves to these principles very well, however this does not guarantee they will fit your need, or perform to the level of some COTS/Closed Source options.
> 
> Additionally, you need to consider that properly integrating your environment is not just about purchasing / implementing an ESB. This is just one component of the overall solution, which should consider the need for MDM, Data Architecture and Canonicalisation, Portals, Business Rules, Registry/Repository, Modelling Tools, Business Process Management and Orchestration, amongst others. The commercial vendors will be more than willing to sell you complete stacks for these things, but be prepared to do things their way, and have a certain amount of lock-in to that vendor.
> 
> As you work there, I'm sure you realise that Microsoft is very Microsoft focused, so is only really sensible for Microsoft only environments. However, your recent deal with SOPERA now gives support for both native .NET and Java environments. Maybe there is hope…
> 
> Oracle and SAP have some good concepts, but unfortunately they are again very insular &#8211; if you only have SAP then XI will be wonderful for you, same with Oracle Fusion and their AIA offering &#8211; extending outside the environment is possible, but often ugly.
> 
> IBM is (imho) conceptually better at being application/solution agnostic, as it doesn't sell applications to integrate, therefore focuses on supporting a diverse range of approaches. However, they have a history of a somewhat convoluted and overlapping solution set.
> 
> Webmethods provides you with a well integrated set of tools and features, again application agnostic, though it's sweet spot is Process Management, and isn't the most open/standards compliant.
> 
> If a company is really strapped for cash… then an open source option may be best. But you may have to work harder to get a complete solution up and running.
> 
> You can get all of the components to integrate your environment for free &#8211; but you have to implement them, they may not be that pretty/easy to use, or performant, and if you choose not to pay for ongoing support then you are putting your company at risk.
> 
> You also have to ask yourself if you are really doing pure SOA, or whether you are needing to do application integration…
> 
> above all, the right architecture will win over a technology choice 

So, the answer was as you would expect for a general Q&A site &#8211; relatively simple/brief (there is a post size limit!) and a little on the generic side to cater for the limited information available. 

I touch briefly on a couple of points I want to highlight here. 

**The first point** being the potential benefit but also risk of using Open Source integration technologies. Put simply, open source's core principles are very much aligned with those of integration, and as such makes an excellent candidate for the technology that glues together all of our other bits and pieces of technology. But, as it is in it's relative infancy, there are some potential risks. These are rapidly being addressed technically, and given a few more years of well rounded practical implementations, many of these issues will be addressed. Although I wont go into detail in this post, I suggest having a look at some of the open source options available, including:

[SOPERA][2] &#8211; very open modular design, with a strong plugin architecture, with integrations to many other software components, including Microsoft .NET &#8211; this is the product behind Eclipse Swordfish.
  
[JBoss][3] &#8211; a well known middleware/application server vendor with parent company Redhat. A well rounded offering, though slightly immature ESB.

There are others (e.g. Apache FUSE), though I'll save the details for another day &#8211; check out the above and see how it may suit your needs.

**The second point** is the question of whether you are really doing pure SOA or not, or whether you're either doing application integration (not too dissimilar in objective, but different in concept) or just &#8216;web services'. Have a think about that… what exactly is SOA to you? And do you think everyone else thinks the same? Feel free to post your thoughts below…

_The full Q&A from linkedin here:_
  
http://www.linkedin.com/answers/technology/enterprise-software/TCH_ENT/441250-2894850?browseIdx=1&sik=1239049806947

 [1]: http://en.wikipedia.org/wiki/Enterprise_service_bus
 [2]: http://www.sopera.com/
 [3]: http://www.jboss.com/products/platforms/soa/