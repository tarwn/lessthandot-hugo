---
title: AngularJS vs Knockout – Introduction (1 of 8)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-07T17:39:00+00:00
excerpt: "I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. There are already plenty of posts out there comparing AngularJS and Knockout. I have been slowly reading through all the comparisons I could find, but unfortunately I keep running into cases where the posts are too high level, miss capabilities I need, or have errors that undermine my trust in the rest of the post."
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-introduction-1/
views:
  - 54544
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - $script.js
  - angularjs
  - director
  - durandal
  - finch
  - jasmine
  - knockout
  - mvc
  - mvvm
  - requirejs
  - sammyjs

---
I&#8217;m reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. There are already plenty of posts out there comparing [AngularJS][1] and [Knockout][2]. I have been slowly reading through all the comparisons I could find, but unfortunately I keep running into cases where the posts are too high level, miss capabilities I need, or have errors that undermine my trust in the rest of the post. 

The thing is, Knockout and AngularJS are attempting to solve two different problems. One is simply an MVVM binding framework, the other is a SPA-in-a-box solution. So instead of trying to directly compare the two frameworks, I&#8217;ve outlined the capabilities I need and will review how well each of the frameworks meets those capabilities. Where one library does not meet a particular set of needs, I&#8217;ll look at a common solution that people use with that library. Since Knockout is purely a databinding library, I expect to have to pull in others when it&#8217;s time to talk about routing, modules, and unit testing, while I shouldn&#8217;t have to do this as much with AngularJS.

Here are the capabilities I need:

<ul style="padding-left: 2em;">
  <li>
    Data binding &#8211; bind HTML elements to JavaScript data models
  </li>
  <li>
    Validation &#8211; validation of raw inputs by applying rules for fields or model properties
  </li>
  <li>
    Serialization &#8211; easy method for serializing models to POST to server-side APIs
  </li>
  <li>
    Templating &#8211; define HTML templates for re-usable complex collections of HTML
  </li>
  <li>
    Modules + DI &#8211; keep my javascript files separate, help me order them properly, manage dependencies for me
  </li>
  <li>
    Automated Testing &#8211; Exploring unit testing and possibilities for higher level testing
  </li>
  <li>
    SPA Routing/History &#8211; make it easy for me to route between views in a single page app, with history/deep linking
  </li>
</ul>

Before I dive into either of these libraries, though, I need some assurance that they will support the browsers I need, won&#8217;t get me into an odd licensing situation, and have sizable communities maintaining them. Here is a list of all the libraries I&#8217;ve incorporated in the series:

<table class="tables" style="border-collapse: collapse">
  <tr>
    <th>
      Package
    </th>
    
    <th>
      License
    </th>
    
    <th>
      Current Version
    </th>
    
    <th>
      Latest Update
    </th>
    
    <th>
      Contributors
    </th>
  </tr>
  
  <tr>
    <td>
      <a href="http://angularjs.org/">AngularJS</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      1.0.8, 1.2.0 rc 2
    </td>
    
    <td>
      2 Days Ago
    </td>
    
    <td>
      392
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://knockoutjs.com/">Knockout</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      2.3.0, 3.0.0 beta
    </td>
    
    <td>
      3 Days Ago
    </td>
    
    <td>
      38
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/ifandelse/Knockout.js-External-Template-Engine">Knockout.js External Template Engine</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      2.0.5
    </td>
    
    <td>
      A year ago
    </td>
    
    <td>
      3
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://requirejs.org/">RequireJS</a>
    </td>
    
    <td>
      MIT or BSD
    </td>
    
    <td>
      2.1.8
    </td>
    
    <td>
      6 Days Ago
    </td>
    
    <td>
      53
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://sammyjs.org/">SammyJS</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      0.7.4
    </td>
    
    <td>
      6 Months Ago
    </td>
    
    <td>
      47
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://durandaljs.com/">Durandal</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      2.0.0
    </td>
    
    <td>
      1 month ago
    </td>
    
    <td>
      27
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://stoodder.github.io/finchjs/">Finch.js</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      0.5.13
    </td>
    
    <td>
      5 months ago
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/flatiron/director">flatiron director</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      1.2.0
    </td>
    
    <td>
      4 months ago
    </td>
    
    <td>
      42
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/ded/script.js/">$script.js</a>
    </td>
    
    <td>
      ??
    </td>
    
    <td>
      N/A (source)
    </td>
    
    <td>
      5 months ago
    </td>
    
    <td>
      8
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://pivotal.github.io/jasmine/">Jasmine</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      1.3.1
    </td>
    
    <td>
      18 hours ago
    </td>
    
    <td>
      49
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/derickbailey/jasmine.async">Jasmine.Async</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      0.1.0
    </td>
    
    <td>
      1 year ago
    </td>
    
    <td>
      1
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/iammerrick/Squire.js/">Squire.js</a>
    </td>
    
    <td>
      MIT
    </td>
    
    <td>
      N/A (source)
    </td>
    
    <td>
      2 months ago
    </td>
    
    <td>
      6
    </td>
  </tr>
</table>

_Latest Update + Current Version as of when I added the table row._

Browser Compatibility:

<table class="tables" style="border-collapse: collapse">
  <tr>
    <th>
      Package
    </th>
    
    <th>
      Browsers
    </th>
  </tr>
  
  <tr>
    <td>
      <a href="http://angularjs.org/">AngularJS</a>
    </td>
    
    <td>
      Not Documented? (<a href="http://docs.angularjs.org/guide/ie">extra requirements</a> for IE8-)
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://knockoutjs.com/">Knockout</a>
    </td>
    
    <td>
      IE 6+, Firefox 2+, Safari (desktop/mobile), Chrome, Opera
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/ifandelse/Knockout.js-External-Template-Engine">Knockout.js External Template Engine</a>
    </td>
    
    <td>
      ??
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://requirejs.org/">RequireJS</a>
    </td>
    
    <td>
      IE 6+, Firefox 2+, Safari 3.2+, Chrome 3+, Opera 10+
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://sammyjs.org/">SammyJS</a>
    </td>
    
    <td>
      IE 8+, Firefox 3+, Safari 3+, Chrome 5+, Opera 10+, Mobile WebKit
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://durandaljs.com/">Durandal</a>
    </td>
    
    <td>
      Not Documented?
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://stoodder.github.io/finchjs/">Finch.js</a>
    </td>
    
    <td>
      Not Documented?
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/flatiron/director">flatiron director</a>
    </td>
    
    <td>
      ??
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/ded/script.js/">$script.js</a>
    </td>
    
    <td>
      IE 6+, Firefox 2+, Safari 3+, Chrome 9+
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="http://pivotal.github.io/jasmine/">Jasmine</a>, <a href="https://github.com/derickbailey/jasmine.async">Jasmine.Async</a>
    </td>
    
    <td>
      Not client facing. Supports a wide range of browsers, including PhantomJS. Chutzpah plugin for Visual Studio (no ncrunch yet though)
    </td>
  </tr>
  
  <tr>
    <td>
      <a href="https://github.com/iammerrick/Squire.js/">Squire.js</a>
    </td>
    
    <td>
      Also not client facing
    </td>
  </tr>
</table>

I&#8217;ll explore each capability in both frameworks as well as my opinions and frustrations along the way. Then when the whole series is posted, I&#8217;ll offer a final set of opinions. The plan is to post daily (weekdays) until I get to the end, so keep an eye out on the site or follow me on [twitter][3] for updates.

I haven&#8217;t actually written the last post yet, so you have plenty of time to color my opinion and point out where I did things wrong as I roll out each post 🙂

<div style="background-color: #DDDDDD; padding: 8px; width: 400px;">
  <h3>
    Knockout vs AngularJS
  </h3>
  
  <ul style="margin: 0px; padding: 4px;">
    <li>
      <b>Introductory Post</b>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-data-binding-2">Data Binding</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-validation-3">Validation</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-serialization-4">Serialization</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-templating-5">Templating</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-modules-and-di-6">Modules + Dependency Injection</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-automated-testing-7">Automated Testing</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/AJAX/angularjs-vs-knockout-spa-routing-history-8">SPA Routing/History</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-final-thoughts-9">Final, Final Thoughts</a>
    </li>
  </ul>
</div>

 [1]: http://angularjs.org/
 [2]: http://knockoutjs.com/
 [3]: http://twitter.com/tarwn "Follow @tarwn on twitter"