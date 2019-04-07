---
title: 'LaunchReady: Focus on the Customer'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2018-03-01T14:41:48+00:00
ID: 8976
url: /index.php/uncategorized/launchready-focus-on-the-customer/
views:
  - 2711
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
When I started LaunchReady, it didn&#8217;t even have a name. I had a general idea of what _I_ wanted a system to do, but didn&#8217;t have a good handle on the target audience and what _they_ would want. I had some general ideas on how to build the software affordably, but nothing with a total row at the bottom. I was excited to jump in and try to build some of the I personally wanted, but realized that led to a system that I could only describe to myself (and likely minus some very obvious features, as well).

<div style="border: 2px solid #ddd; border-left-width: 16px; margin: 1em 0; padding: 1em;">
  <h3>
    LaunchReady Series
  </h3>
  
  <p>
    1. <a href="/index.php/uncategorized/meet-launchready-a-saas-product-that-almost-was/">LaunchReady: Meet the SaaS Product I Almost Built</a><br /> Introduction and backlog ideas I hope folks will incorporate into their products
  </p>
  
  <p>
    2. <u>LaunchReady: Focus on the Customer</u><br /> Some practices that helped me see through the customer eyes
  </p>
  
  <p>
    3. <a href="/index.php/uncategorized/launchready-dont-get-distracted-getting-stuff-done/">LaunchReady: Don&#8217;t Get Distracted, Getting Stuff Done</a><br /> Ignoring the distracting cool ideas to get work done
  </p>
</div>

Initially, I was my target customer. This would be the product I was looking for when I scaled the engineering team at [PrecisionLender][1]. I had wanted a tool that would take all of the best learning from our core team and make it available to the new folks as they were getting up to speed. For reference, we were quadrupling the product development organization, launching a public API and third-party integrations, replatforming the entire 250KLOC front-end, refining existing features, and we added a newborn along the way for extra difficulty level&#8230;it was a busy time.

I wanted a product that focused on the fast growing, development organization that couldn&#8217;t afford to go learn and build and support a whole second product to test their first one and didn&#8217;t have experienced test automation folks on board already. And I wanted to challenge the conventions that UI Automation must be slow and fragile and hard to change.

## Practices that helped define the product

At the beginning, not much of the system was clear beyond some of the features I wanted. There wasn&#8217;t a cohesive story I could tell someone, much less a product I could try to sell. There also wasn&#8217;t a clear path from that set of features to a fully working system, I could only explore so many details in my head. So it became important to identify what that system would offer to external customers, how I would explain the problem it was trying to solve, how to make it viable form a financial perspective, and what all of those necessary features were that I wasn&#8217;t thinking of while it was only in my head.

### The Marketing Site

I started building the marketing site very early. This forced me to think through questions about the type of folks the product was serving, the problems they would be searching for online, and the features that had to be included from day 1 of even a pilot offering. This drove changes from the terminology, to the flow between screens, to the basic data structures behind the system.

[<img src="/wp-content/uploads/2018/02/launchreadypost_02-600x311.png" alt="LaunchReady - Deploy with Confidence" width="600" height="311" class="aligncenter size-medium-width wp-image-8943" srcset="/wp-content/uploads/2018/02/launchreadypost_02-600x311.png 600w, /wp-content/uploads/2018/02/launchreadypost_02-300x155.png 300w, /wp-content/uploads/2018/02/launchreadypost_02-768x398.png 768w, /wp-content/uploads/2018/02/launchreadypost_02-579x300.png 579w, /wp-content/uploads/2018/02/launchreadypost_02.png 867w" sizes="(max-width: 600px) 100vw, 600px" />][2]

The marketing site is still up, if you&#8217;re interested: <https://www.launchready.co>.

Many people will tell you to put up the Marketing Site and validate the product before you build it. In this case, I built the product at the same time because I was concerned about the complexity and making sure I could connect the message with real functionality (or build exceptions back into the message).

### Creating the Financial Spreadsheet

Building the financial spreadsheets and forecasts forced me to think really hard on what limits or capabilities I was going to include. There are features I wanted to include in the beginning that I realized couldn&#8217;t be included until customer #10-15 for a partial launch, or #30 for a full-scale launch. There was a valuable feedback loop between things the marketing site increased the priority on, what it took to implement them, the impact that had on the financial perspective, and back around again.

<img src="/wp-content/uploads/2018/02/launchreadypost_03-600x248.png" alt="Financial Spreadsheet" width="600" height="248" class="aligncenter size-medium-width wp-image-8944" srcset="/wp-content/uploads/2018/02/launchreadypost_03-600x248.png 600w, /wp-content/uploads/2018/02/launchreadypost_03-300x124.png 300w, /wp-content/uploads/2018/02/launchreadypost_03.png 627w" sizes="(max-width: 600px) 100vw, 600px" />

SaaS financials are a pretty widely talked about subject these days, so finding good models and articles was relatively easy. During development, I was able to keep my costs for the marketing site and production systems under $25/month while having a realistic plan to quickly turn on a pilot, add advertising, and then scale with forecasted production traffic. 

### Plan for customer onboarding

A key goal was to challenge the belief that UI Automation has to be fragile and complex. There is complexity in the implementation details, but I was going to have a target audience that wouldn&#8217;t have that background and needed to be able to get up to s speed and back to what they were doing very quickly (or they would give up and move on).

Besides the usual walkthrough in the docs ([LaunchReady: Getting Started][3]), I added a required, guided walkthrough to the application that led a first time user through adding a new application, their first user scenario, running a manual test, and reading the results from automated tests. It took 5-10 minutes and gave them a hands on introduction to the terminology and screens, in the hopes that this would give them enough tool to build their first real test and get value quickly.

<div id="attachment_8948" style="width: 610px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/02/launchreadypost_07-600x265.png" alt="First Step of Interactive Onboarding Walkthrough" width="600" height="265" class="size-medium-width wp-image-8948" srcset="/wp-content/uploads/2018/02/launchreadypost_07-600x265.png 600w, /wp-content/uploads/2018/02/launchreadypost_07-300x132.png 300w, /wp-content/uploads/2018/02/launchreadypost_07-768x339.png 768w, /wp-content/uploads/2018/02/launchreadypost_07-1024x452.png 1024w, /wp-content/uploads/2018/02/launchreadypost_07-680x300.png 680w, /wp-content/uploads/2018/02/launchreadypost_07.png 1217w" sizes="(max-width: 600px) 100vw, 600px" />
  
  <p class="wp-caption-text">
    First Step of Interactive Onboarding Walkthrough
  </p>
</div>

I was prioritizing heavily to slice the work down for the MVP, but this seemed like a critical piece of the puzzle if I was going to get users succeeding quickly. The 5 minute target forced simplifications and changes elsewhere in the system, but when I look back these were all for the better and I could count on everyone using the system having a common minimum base of knowledge from that onboarding flow.

## Next: Getting Things Done

These 3 activities helped focus on what I needed to do, but not always how to get it done at the end of an already long day. In the next post I&#8217;ll talk to some of the practices I incorporated to stop me from getting distracted by cool ideas (like those in the [first post][4]) and the general drag from working in the evenings (I&#8217;m a morning person).

 [1]: https://precisionlender.com
 [2]: https://www.launchready.co
 [3]: https://www.launchready.co/docs/getting-started/first-time-through/ "LaunchReady: Getting Started"
 [4]: /index.php/uncategorized/meet-launchready-a-saas-product-that-almost-was/