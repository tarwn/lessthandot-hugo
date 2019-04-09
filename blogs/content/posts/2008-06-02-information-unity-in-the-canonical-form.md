---
title: Information Unity in the Canonical Form
author: damber
type: post
date: 2008-06-02T06:50:28+00:00
ID: 37
url: /index.php/architect/designingmultiapplicationsolutions/information-unity-in-the-canonical-form/
views:
  - 7059
rp4wp_auto_linked:
  - 1
categories:
  - Designing Multi-Application Solutions
  - Enterprise Architecture
  - Information and Integration Architecture
tags:
  - canonical
  - data modelling
  - integration
  - standards

---
## So what is a CDM ?… 

A Canonical Data Model is most simply explained as a standardised way of representing data that generically applies to multiple systems and data models &#8211; kind of like the master data model, but for semantics aswell as content. It can be used as a data model standard template for new systems development, or as an intermediary serialised data format &#8211; which is the aspect I'll focus on mostly here.

Here's an Example of using the CDM as an intermediary data format to integrate six systems with different data format requirements: Canonical Data Model

![CDM Example][1]

In this example, where serialised data is being passed between systems, it uses the standardised format as a middle ground, meaning that each adapter only needs to be able to translate into or out of the CDM format (rather than each individual target/source) for it to be able to understand (and therefore integrate) with other systems &#8216;talking' in the same context (e.g. a specific functional domain, like shipments, invoices, orders etc). This creates semantic interoperability between the system by abstracting the differences at the perimeter of the integration solution, and re-using existing components.

## So what ?

It's not always clear when first thinking about this design why it is so important to the implementation of upstream architectures such as SOA, or why this has such impact on everyday integration.. even the more traditional hub based point-to-point integration patterns. But hopefully that realisation is starting to emerge, because it really is key to building truly flexible, seamless and invisible integration solutions.

## Some of the key objectives

  * Maximise Re-Use of Components
  * Minimise Redundant Code
  * Facilitate the concept of &#8216;Plug-And-Play' interfaces
  * Standardise knowledge, terminology and domain concepts (talk the same language)
  * Facilitate SOA through an information architecture (IOA) foundation

## Some of the Key Benefits and Advantages

<span style="font-weight:800;text-decoration:underline;">Connect Once, Integrate Many</span> &#8211; once connected to the information network via the canonical adapter, you can communicate with any other connected system as long as it is in context (e.g. order requests aren't likely to work with Proof of Delivery)… but having said that, things do become somewhat adaptable, even out of context… for example: an order quite often contains customer information and product information, as well as being an outline of the shipment. With this in mind you \*could\* (and that doesn't mean \*should\* 😀 ) feed a customer master or CRM application with the customer information, a Product Master or Stock Management application with the product information &#8216;just-in-time' for the order to be processed should the information not have been provided before (unlikely for the product master, but you get the idea). You could even feed a shipment scheduling system, shipment event tracking system and plenty of others just from that single message (the most valuable might be your data warehouse).

Here's a comparison of point to point integration versus a canonical model:

![point to point vs Canonical][2]

## CDM v Point to Point Comparison

As you can see, the point to point method (bottom image) creates a LOT of components and complexity. Taking this example and doing the Math:

##### Point-To-Point Integration for 3 sending systems and 3 receiving systems:

3 (sources) * 3 (targets) = 9 (components)

##### CDM Based Integration for 3 sending systems and 3 receiving systems:

3 (sources) \* 1 (adapter &#8211; the CDM is now the virtual target) + 3 (targets) \* 1 (adapter &#8211; the CDM is now the virtual source) = 6 (components)

Or put more simply:
  
PTP Integration: sources MULTIPLIED by targets
  
CDM Integration: sources PLUS targets 

This starts to demonstrate itself very quickly when you look at 10 sources and 10 targets… would you prefer to build 20 components, or 100 ?

<span style="font-weight:800;text-decoration:underline;">Standardised Information Architecture Foundation</span> &#8211; the concept and importance of an Information Oriented Architecture (IOA) should not be understated. Architecture within an IT environment is mostly about abstraction, generalisation and standardisation of concepts to deliver sustainable, re-usable solutions and technology. One of the most popular architectures in previous years has been the N-Tier architecture where layers of the solution are packaged into abstracted levels that are &#8216;theoretically' interchangeable etc. Furthermore, the more recent Service Oriented Architecture (SOA) expands / explodes that concept somewhat (there's a bit more to it, but for examples sake..) into a distributed network oriented environment, with the addition of a bus to provide the glue that binds all the interchangeable parts together. In both of these concepts, information should be standardised, or if not possible (e.g. from multiple sources, which SOA is very prone to) then to abstract the differences between the data sources and present (e.g. via the Data Access Tier, Business Data Objects, Composite Services etc) a standardised version.

There are products that will help to do this on a READ basis &#8211; generally called data federation, it allows multiple sources of data to be combined into a single &#8216;view' despite the underlying technologies (e.g. RDBMS, XML, CSV, Web Service, etc). This is great, and a useful tool, but it is not enough to satisfy the needs of a true SOA environment. There are also technologies that will help do this on a more general basis, which are much more applicable, such as business data objects, which can be used to &#8216;mimic' a SOA like environment without all the standards stack. Then you have the SOA concept, which implements data and functionality as services, but really is founded on those data sources, and their role within the solution (sometimes data sources are not the authority on the data). Hence, a solid information architecture is the foundation of your SOA.

Whichever way you look at it, having this standard model helps to bring your enterprise solutions together whilst minimising diversity and maximising re-use.

For example, you could implement a Data Warehouse by simply adding a canonical adapter for the data warehouse feeds, and directing existing data (coming via other canonical adapters) to this point &#8211; if you have 20 or so systems that are already in your network you can start to see how this would make your life easier !

It also helps when designing and developing new applications & data sources, as you can simply implement the CDM template, and you have 80-90% of your data model (there are always application specific data tables required by a system). 

## Some Things to Consider About the Design of a CDM

  * Version Control needs to be closely managed
  * You should be able to change the CDM Format without &#8216;breaking' existing adapters &#8211; otherwise changes will become more and more of a risk/impact
  * Data content (as well as the syntax and semantics) should be standardised within the CDM boundaries (transformed on entry) &#8211; this is where Master Data Management becomes essential &#8211; for example, you want to ensure that timezones are clearly included with time/dates, customer IDs are the same, country codes, currency codes and so on are all the same
  * Technology needs to support then dynamic and flexible nature of this &#8211; XML is a prime candidate, coupled with XSLT, Xpath and Xquery
  * Data Models should be based on REAL LIFE &#8211; not on existing applications or the buzz-de-jour &#8211; speaking to the business is an essential part of building lasting and truly representational models
  * Use Standards where possible &#8211; but don't think there is an off the shelf model and solution that will fit your business perfectly. Things like BODs are interesting and useful, but not necessarily what you need
  * Consider the data serialisation approach &#8211; message centric (like an EDI Message) or entity centric (like a database) &#8211; how normalised do you want that serialised format ? For example a Customer would appear in both orders and invoices (plus many other things) &#8211; do you want &#8216;Customers' to be a separate entity, or an embedded entity ? An entity centric approach is more appropriately served by developing a Semantic Ontology, using technologies such as RDF and OWL.

## So, what next ?

Delivering a CDM can be challenging &#8211; especially when delivered as part of a full information architecture overhaul. My advice ? Take little steps.

Start with a vision &#8211; think about (and ask) what the business really needs from information, then conceptualise, agree and communicate it.

Then (ideally) you want to define an information architecture strategy as thoroughly as possible upfront, so you have a good framework to work from. Try to be as far reaching (in terms of your business units etc) as possible with the analysis for this, but don't get bogged down in specifics &#8211; just get the framework for how things should be done and the strategy around managing, governing, delivering etc. 

Once you have a good framework and strategy, you can start analysing the detail and pulling together a Requirements Analysis Spec and the Architecture/Design for the solution. BUT.. this bit should be done in small manageable chunks &#8211; that way a) you minimise risk of failure b) minimise impact if things do go wrong and c) show fast, measurable results to encourage people to continue to take-up this strategy. I'll post again soon with more detailed steps to this approach

I hope this has either inspired you to look at implementing a Canonical Data Model as part of your enterprise Information Architecture strategy, or enraged you enough to think of some better way…

 [1]: http://www.damber.net/blog/files/imgs/canonical_diagram_simple.gif
 [2]: http://www.damber.net/blog/files/imgs/ptp_v_cdm.gif