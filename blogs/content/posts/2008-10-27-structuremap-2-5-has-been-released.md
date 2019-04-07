---
title: Dependency Injection Tool StructureMap 2.5 Has Been Released
author: SQLDenis
type: post
date: 2008-10-27T11:08:47+00:00
ID: 183
url: /index.php/desktopdev/mstech/structuremap-2-5-has-been-released/
views:
  - 5647
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - .net
  - 'c#'
  - dependency injection
  - structuremap

---
StructureMap is a Dependency Injection tool written in C# for .NET development. StructureMap is also a generic &#8220;Plugin&#8221; mechanism for flexible and extensible .NET applications.

**The new functionality in StructureMap 2.5:** 

  * Completely revamped Assembly scanning options 
  * Cleaner, more predictable way to initialize a Container.&#160; StructureMapConfiguration is now deprecated, please use ObjectFactory.Initialize().
  * Optional setter injection 
  * All new abilities to query the configuration of a Container 
  * The ability to use StructureMap with ZERO Xml or attributes by default 
  * The ability to add services at runtime. You can now programmatically add an entire Assembly at runtime for modular applications that might not want all services to be loaded at startup. 
  * An auto mocking container based on Rhino Mocks 3.5. I was a doubter on the validity of AMC, but I'm sold now that I've used it 
  * Contextual object construction 
  * More sophisticated auto wiring rules 
  * Supporting NameValueCollection and IDictionary types 
  * Far more extensibility 
  * Interception and post processing hooks for you AOP enthusiasts. StructureMap will NOT include its own AOP engine, but will allow you to use the runtime AOP technique of your choice. 
  * More configuration options in both Xml and the Fluent Interface. Completely revamped the Registry DSL. 
  * More options for modular configuration (mix and match Xml configuration or Registry's at will) &#8212; which basically had to trigger: 
  * Completely revamped diagnostics, including the Environment Testing support 
  * Transparent creation of concrete types that are not explicitly registered 
  * Create objects with explicit arguments passed to the container 
  * Use the underlying Container independently of ObjectFactory 
  * Pluggable auto registration with your own custom Type scanning policies 
  * StructureMap is now strong named (thanks to Steve Harman) 
  * Pull configuration from the App.config (thanks to Josh Flanagan) 
  * Generics fixes (thanks to Derrick Rapp) 

**Download it here: http://sourceforge.net/projects/structuremap**