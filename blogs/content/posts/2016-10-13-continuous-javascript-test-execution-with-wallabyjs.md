---
title: Continuous Javascript Test Execution with WallabyJS
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-10-13T19:53:07+00:00
ID: 4676
url: /index.php/webdev/continuous-javascript-test-execution-with-wallabyjs/
views:
  - 4050
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - jasmine
  - javascript
  - requirejs
  - unit testing
  - wallabyjs

---
After working with NCrunch building and running tests in the background for the last several years, it feels like something is broken when I have to wait for test results or push a button to start running them. JavaScript runners just didn't feel like they provided the same level of development feedback, whether they were command-line runners with gulp tasks, plugins like Chutzpah, or dedicated runners like Karma.

_I've posted previously on both [NCrunch][1] and [Karma][2], test runners that run .Net and Javascript code continuously behind the scenes as you develop._

[WallabyJS][3] is like NCrunch for Javascript. It radiates test statuses directly in your IDE as you edit your code, letting you know what's workign and not working without any extra action. No switching to a secondary screen or manually running and waiting for results. It has wide support, integrating with the IntelliJ platform, Visual Studio, Visual Studio Code, Sublime, and more. 

I used this sample project throughout the post: [github.com/tarwn/townthing][4]. It is a small sandbox project that uses RequireJS and had been configured for Karma as well as having a Jasmine SpecRunner for running the tests in the browser. Hopefully this means Wallaby will be able to slide right in.

## From Zero to Wallaby, in Visual Studio Code

First step, open Visual Studio Code and install the extension: ext install wallaby-vscode

Wallaby has a really easy to follow “getting started” guide that I mostly ignored: <https://wallabyjs.com/docs/config/overview.html>

I didn't pay a lot of attention, but jumped straight to pushing Ctrl+Shift+R, R after installing the extension. It prompted me to identify a config file (I created an empty “wallaby.js” file), then upset my firewall briefly by running node.js (which I allowed).

I then created my wallaby.js configuration using a short example of using wallaby with RequireJS: [github.com/wallabyjs/wallaby-requirejs-sample][5]

**wallaby.js**

```javascript
module.exports = function (wallaby) {
  return {
    files: [
      { pattern: 'town/js/lib/require-2.1.11.js', instrument: false },
      { pattern: 'town/js/lib/*.js', load: false },
      { pattern: 'town/js/src/*.js', load: false },
      { pattern: 'town/js/test/test-main.js' }
    ],

    tests: [
      { pattern: 'town/js/test/*.spec.js', load: false },
    ],

    testFramework: 'jasmine'
  };
};
```
This identifies all the files and tests for wallaby, but tells it not to actually load anything but RequireJS and and my RequireJS configuration (tets-main.js).

Currently, my test main is focused on running karma, but we can easily switch it to be able to run either.

**test-main.js**

```javascript
var tests = [];

var baseUrl = '';
var isUsingKarma = (window.__karma__ != undefined);
var isUsingWallaby = (wallaby != undefined);

if(isUsingKarma){
  baseUrl = '/base/src';
  for (var file in window.__karma__.files) {
    if (window.__karma__.files.hasOwnProperty(file)) {
      if (/spec\.js$/.test(file)) {
        tests.push(file);
      }
    }
  }
}
else if(isUsingWallaby){
  baseUrl = '/town/js/src';
  wallaby.delayStart();
  tests = wallaby.tests;  
}

requirejs.config({
  // Karma serves files from '/base'
  baseUrl: baseUrl,

  paths: {
    "knockout": "../lib/knockout-3.0.0",
    "Squire": "../lib/Squire"
  }
});

// Let's get started!
require(tests, function(){

  if(isUsingKarma)
  	window.__karma__.start();
  else if(isUsingWallaby)
    wallaby.start();

});
```
The key parts are to ensure I delay wallaby to start, set the test collection and baseUrl for requireJS, then start the tests inside a require statement at the end.

_Note: I did manage to completely lock up Visual Studio Code while updating the test-main file, but I'm not sure if that was VS Code's fault or Wallaby's_

And there we go. As I type my code in the editor, I get instant notifications of errors (including some handy hover boxes with details) and my test markers turn green/red as I fix and break tests.

Here is the working code:
  


<div id="attachment_4678" style="width: 810px" class="wp-caption alignleft">
  <a href="/wp-content/uploads/2016/10/PassingTests.png"><img src="/wp-content/uploads/2016/10/PassingTests-1024x338.png" alt="Passing Tests w/ Inline Markers and Wallaby Console" width="800"" class="size-large wp-image-4678" srcset="/wp-content/uploads/2016/10/PassingTests-1024x338.png 1024w, /wp-content/uploads/2016/10/PassingTests-300x99.png 300w" sizes="(max-width: 1024px) 100vw, 1024px" /></a>
  
  <p class="wp-caption-text">
    Passing Tests w/ Inline Markers and Wallaby Console
  </p>
</div>

and now when I add a “+ 1” to the end of the line without even saving the file, the test marker turns red and I get instant results in the console below:
  


<div id="attachment_4679" style="width: 810px" class="wp-caption alignleft">
  <a href="/wp-content/uploads/2016/10/FailingTests.png"><img src="/wp-content/uploads/2016/10/FailingTests-1024x436.png" alt="Failing Tests - Red Marker, Clickable Console Details" width="800" class="size-large wp-image-4679" srcset="/wp-content/uploads/2016/10/FailingTests-1024x436.png 1024w, /wp-content/uploads/2016/10/FailingTests-300x127.png 300w" sizes="(max-width: 1024px) 100vw, 1024px" /></a>
  
  <p class="wp-caption-text">
    Failing Tests – Red Marker, Clickable Console Details
  </p>
</div>

This is much closer to the experience you get with NCrunch and Visual Studio Code is actually a more limited wallaby experience than other editors. The setup was quicker than karma, even though I've setup karma more times. If you work in Javascript daily, this is definitely worth a long look.

 [1]: /index.php/enterprisedev/unittest/reducing-code-build-test-friction/
 [2]: /index.php/webdev/uidevelopment/javascript/continuous-javascript-testing-with-karma/
 [3]: https://wallabyjs.com/
 [4]: https://github.com/tarwn/townthing
 [5]: https://github.com/wallabyjs/wallaby-requirejs-sample