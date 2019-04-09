---
title: Unit Testing with Jasmine 2.0 and Require.JS
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-03-04T13:41:12+00:00
ID: 2475
url: /index.php/webdev/uidevelopment/javascript/unit-testing-with-jasmine-2-0-and-require-js/
views:
  - 19862
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - Unit Testing
tags:
  - jasmine
  - javascript
  - requirejs

---
Jasmine 2.0 has changed how it loads and executes tests, using a boot script now to handle the details. If you try to plug some require() calls into the sample SpecRunner.html page, Jasmine will be done and finished before the require() statement loads the test modules and their dependencies.

The problem is that RequireJS loads the dependencies asynchronously, but the standard boot script for Jasmine runs when window.onload is called. So how do we fix it?

## Option 1: Call window.onload Ourselves

One option to solve this is to simply call window.onload again:

```html
```
But that's icky and causes you to have two test bars across the screen (and probably doesn't work well with other reporters either).

[<img src="/wp-content/uploads/2014/02/JasmineDoubleFail.png" alt="JasmineDoubleFail" width="726" height="182" class="aligncenter size-full wp-image-2477" srcset="/wp-content/uploads/2014/02/JasmineDoubleFail.png 726w, /wp-content/uploads/2014/02/JasmineDoubleFail-300x75.png 300w" sizes="(max-width: 726px) 100vw, 726px" />][1]

Yeah, that's special.

## Option 2: Custom Boot Script

Or we can fix the root cause, the fact that the tests are running on window.onload and that doesn't play well with AMD. The boot script included with Jasmine is supposed to be a template that can be customized to your own needs, so let's take advantage of that. Copying the existing boot script, we can replace the section that registers the tests to onload with one that will add a callable method to the window:

**boot-without-onload.js**

```javascript
/**
   * ## Execution
   *
   * No onload, only on demand now
   */

  window.executeTests = function(){
    htmlReporter.initialize();
    env.execute();
  };
```
And then update our SpecRunner to include this replacement boot script and require the test files prior to executing the tests:

```html
```
And there we go, Jasmine is now working exactly the same as if we were running without RequireJS (and had pasted 500 script tags in the file).

 [1]: /wp-content/uploads/2014/02/JasmineDoubleFail.png