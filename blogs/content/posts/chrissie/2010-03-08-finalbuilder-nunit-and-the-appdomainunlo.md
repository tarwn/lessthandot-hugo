---
title: Finalbuilder, Nunit and the AppDomainUnloadedException
author: Christiaan Baes (chrissie1)
type: post
date: 2010-03-08T11:04:42+00:00
ID: 720
url: /index.php/itprofessionals/softwareandconfigmgmt/finalbuilder-nunit-and-the-appdomainunlo/
views:
  - 3798
categories:
  - Software and Configuration Management
tags:
  - appdomainunloadedexception
  - finalbuilder
  - nunit

---
I was running into this error when running my [Nunit][1]-tests.

> Executing : C:Program FilesNUnit 2.5.2binnet-2.0nunit-console.exe
  
> With Command Line : TDB2009.View.Test.C.dll
  
> NUnit version 2.5.2.9222
  
> Copyright (C) 2002-2009 Charlie Poole.
  
> Copyright (C) 2002-2004 James W. Newkirk, Michael C. Two, Alexei A. Vorontsov.
  
> Copyright (C) 2000-2002 Philip Craig.
  
> All Rights Reserved.
> 
> Runtime Environment &#8211;
     
> OS Version: Microsoft Windows NT 5.1.2600 Service Pack 2
    
> CLR Version: 2.0.50727.3068 ( Net 2.0.50727.3068 )
> 
> ProcessModel: Default DomainUsage: Single
  
> Execution Runtime: net-2.0.50727.3068
  
> ..
  
> Tests run: 2209, Errors: 0, Failures: 0, Inconclusive: 0, Time: 350,7344622 seconds
    
> Not run: 0, Invalid: 0, Ignored: 0, Skipped: 0
> 
> Unhandled Exception:
  
> System.AppDomainUnloadedException: Attempted to access an unloaded AppDomain.
     
> at System.AppDomain.get_FriendlyName()
     
> at NUnit.Util.DomainManager.DomainUnloader.Unload()
     
> at NUnit.Util.TestDomain.Unload()
     
> at NUnit.ConsoleRunner.ConsoleUi.Execute(ConsoleOptions options)
     
> at NUnit.ConsoleRunner.Runner.Main(String[] args)
  
> Program returned code : -1073741819

As you can see, I have 0 failures and 0 errors but still it exits with a negative exitcode, which makes finalbuilder decide that it failed. But in this case, it is more of a bug, whether or not this is in my code or Nunit is beside the point. But the code runs just fine in the Nunit GUI and in the [R#][2] testrunner. So I wasn&#8217;t going to let it beat me in any way.

You can find all sorts of solutions to this problem but none of them seemed to help me any further. So I looked for help elsewhere and found it on [Craig Murphy&#8217;s site][3].

The solution was to create a custom Nunit runner and have it only react on errors or failures not on the exitcode. 

It is pretty easy to do in [finalbuilder][4] 5, once you know how ;-).

You start by creating an **Execute Program** action from the Windows OS group. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/finalbuilder/finalbuilder1.png" alt="" title="" width="558" height="499" />
</div>

In **Program File** you fill in the location of your nunit-console. **Start In** is the location of the assembly you wanna test and **Parameters** will have the name of the assembly. As you can see, I disabled **Program exit code, must be** because I don&#8217;t need it.

Then we add a **Define XML Document** action (XML group) to the Execute Program action we defined before. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/finalbuilder/finalbuilder2.png" alt="" title="" width="560" height="499" />
</div>

In **XML Document Name** we specify a name that we will use in later actions and we fill in the **Load document from file**. By default, Nunit will create an XML file in the assembly folder that is named TestResult.xml, so you use that.

Now we add a variable that we will use in the next step.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/finalbuilder/finalbuilder6.png" alt="" title="" width="714" height="530" />
</div>

We call it TestFailures and leave all the other things default.

Then we add a **Read XML value to Variable** action.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/finalbuilder/finalbuilder3.png" alt="" title="" width="560" height="497" />
</div>

In **XML File** we select **XML Document** and choose the one we just created from the list. We fill in the **XPath to Node** to start looking in //test-results and we **Read an attribute of the XPath Node** named failures. And in Variable To Set we pick the TestFailures variable we just created.

Nearly done. Last step is to add an if..Then action and add a Raise Exception action to that If .. Then Action. Both can be found in the Flow Control group.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/finalbuilder/finalbuilder4.png" alt="" title="" width="559" height="498" />
</div>

For the **Left-Hand Value** we type in %Test-Failures% which is the name of the variable we made earlier. **Operator** is greater than and **Right-Hand Value** is 0. Meaning that it will Raise an Exception if we get 1 or more failures.

We should then see the following.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals/finalbuilder/finalbuilder5.png" alt="" title="" width="907" height="86" />
</div>

You should add a variable and if then for errors too just to be sure.

This should solve our problem. Or better said, this works around our problem.

 [1]: http://www.nunit.org/
 [2]: http://www.jetbrains.com/resharper/
 [3]: http://www.craigmurphy.com/blog/?p=654
 [4]: http://www.finalbuilder.com/