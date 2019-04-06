---
title: AngularJS vs Knockout – Automated Testing (7 of 8)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-15T13:00:00+00:00
excerpt: "I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. As we get into projects that are larger than a few small views and routes, the ability to add automated testing becomes important.  Unit testing p&hellip;"
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-automated-testing-7/
views:
  - 22178
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - angularjs
  - jasmine
  - knockout
  - unit testing
  - you better mock yourself

---
I&#8217;m reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. As we get into projects that are larger than a few small views and routes, the ability to add automated testing becomes important. Unit testing provides a safety net against future us screwing up the code that present us is writing, can be used before we write the code (TDD) or after, and helps us keep some of the complexity in check as the project size grows and ages. Let&#8217;s put AngularJS and Knockout under test.

<div style="background-color: #eeeeee; padding: 1em;">
  This is the seventh of eight posts looking at the capabilities of knockout and Angular. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1" title="AngularJS vs Knockout - Introduction">introduction post</a>, I outlined the capabilities that I am evaluating for. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-modules-and-di-6" title="AngularJS vs Knockout - Modules and DI">sixth post</a>, I looked at Modules and Dependency injection, an important lead up to this post, which intends to jump into unit testing those modules.
</div>

All of the examples presented throughout the series are available in the [tarwn/AngularJS-vs-Knockout][1] repository on github.

In both sections I&#8217;ll be using [Jasmine 1.3.1][2], the focus is on how the libraries are to test, so this will provide a somewhat level playing field. We&#8217;ll be testing the modules from the previous post, so I&#8217;ll also be including [RequireJS][3] for the Knockout side of things, but skipping script.js and going with standard script tags for the Angular side (aka, being lazy). I&#8217;ll also be pulling in [Squire.js][4] and [Jasmine.Async][5] to add mocking for RequireJS and asynchronous shortcut methods for Jasmine, respectively.

Both sets of tests are tested from a single Jasmine testrunner, in the github repository: [SpecRunner.html][6]

## Unit Testing AngularJS

Unit Testing is of huge importance to the AngularJS team, for which I can&#8217;t applaud them enough. It is constantly mentioned in documentation and tutorials and has resulted in the [karma][7] test runner, a tool I&#8217;m impatient to start playing with but unfortunately have not yet had time for.

### AngularJS Modules Under Test

In the [previous post][8], we had sampleApp and sampleServices modules that we moved into external files, using script.js as an asynchronous module loader. Other than creating a copy of the files and putting them in a new folder (js/UnitTesting) specific to this post, no modifications have been made.

Relevant sections of [SpecRunner.html][6]

<pre>&lt;html&gt;
&lt;head&gt;
    &lt;!-- ... --&gt;

    &lt;!-- Angular files libraries --&gt;
    &lt;script src="Angular/js/lib/angular-1.0.8.min.js"&gt;&lt;/script&gt;
    &lt;script src="Angular/js/lib/angular-mocks.js"&gt;&lt;/script&gt;
    &lt;!-- Angular source files --&gt;
    &lt;script src="Angular/js/UnitTesting/sampleApp.js"&gt;&lt;/script&gt;
    &lt;script src="Angular/js/UnitTesting/sampleServices.js"&gt;&lt;/script&gt;
    &lt;!-- Angular specs --&gt;
    &lt;script src="Angular/UnitTestingSpecs.js"&gt;&lt;/script&gt;

    &lt;!-- ... --&gt;</pre>

The specs file then handles mocking the service for the controller and defines the tests I want to run:

<pre>//  borrowed heavily from http://www.benlesh.com/2013/05/angularjs-unit-testing-controllers.html
describe("Angular", function () {
    describe("Testing the ModuleDIController", function () {
        var $scope = null;

        var expectedServiceResponse = [{ name: "A", number: 123 }];
        var mockService = {
            getList: function () { return expectedServiceResponse; }
        };

        beforeEach(module('sampleApp'));
        beforeEach(inject(function ($rootScope, $controller) {
            $scope = $rootScope.$new();
            $controller('ModuleDIController', {
                $scope: $scope,
                ListOfItemsService: mockService
            });
        }));

        // ... tests here ...
    });
});</pre>

If you&#8217;re going to work with AngularJS, read everything on [Ben Lesh&#8217;s][9] ([@BenLesh][10]) site. It helped me tremendously for both this post and the custom validation section of the validation post. 

The spec file starts off by defining both a mock service and the expected response it is going to return. Before each test I load a fresh sampleApp module, ensuring a clean starting point. Then I use [inject][11] to create an $injector that will be used for resolving references in my tests, which resolves the ModuleDIController by passing in the provided scope and my mock service.
  
That last part works, but honestly I only sort of understand what it&#8217;s doing. Even after several more readings of the pages on the injector and the mock.inject call, I&#8217;m still not 100% sure I grasp more than the basic operation.

The tests themselves are pretty straightforward at that point:

<pre>it('should start with an empty list of items', function () {
    expect($scope.listOfItems).toEqual([]);
});

it('should populate list from service when fillItems() is called', function () {
    $scope.fillItems();
    expect($scope.listOfItems).toEqual(expectedServiceResponse);
});</pre>

And there we have it, verification that our controller uses the service properly to fill it&#8217;s local collection.

This may not be that complicated a test, but once we have the basic components together, extending it to more complex cases is pretty straightforward.

## Unit Testing Knockout/RequireJS

Unit testing modules defined with RequireJS is challenging. There are a couple libraries out there that people have built to inject mocks and the documentation is sparse for all the examples I found. I chose SquireJS due to having slightly more documentation than the others, but it still took a few iterations to get it working (then I lost those changes and had to redo them after not looking at the code for a couple weeks).

### Knockout/RequireJS Modules Under Test

In the [previous post][8], we had sampleApp/ModuleDIModel and sampleServices/ListOfItemsService modules being loaded by RequireJS. Like the AngularJS example, the only change I have made is to move a copy of these files into a folder specific for this post (js/UnitTesting).

Relevant sections of [SpecRunner.html][6]

<pre>&lt;html&gt;
&lt;head&gt;
    &lt;!-- ... --&gt;

    &lt;!-- Knockout + RequireJS files --&gt;
    &lt;script src="Knockout/js/lib/knockout-2.3.0.min.js"&gt;&lt;/script&gt;
    &lt;script src="Knockout/js/lib/require-2.1.8.min.js"&gt;&lt;/script&gt;
    &lt;!-- Knockout specs --&gt;    
    &lt;script src="Knockout/UnitTestingSpecs.js"&gt;&lt;/script&gt;

    &lt;!-- ... --&gt;
  &lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>

Like the AngularJS example, the specs file is responsible for supplying the mocks and defining the tests:

<pre>require.config({
    baseUrl: "Knockout/js/UnitTesting",
    paths: {
        "Squire": "../../js/lib/Squire"
    }
});

describe("Knockout", function () {
    
    describe("Testing the ModuleDIModel", function () {
        var async = new AsyncSpec(this);
        var viewmodel = null;

        var expectedServiceResponse = [{ name: "A", number: 123 }];
        var mockService = {
            getList: function () { return expectedServiceResponse; }
        };

        async.beforeEach(function (done) {
            require(['Squire'], function (Squire) {
                var squire = new Squire();
                squire.mock("sampleServices/ListOfItemsService", mockService)
                      .require(["sampleApp/ModuleDIModel"], function (ModuleDIModel) {
                          viewmodel = new ModuleDIModel();
                          done();
                      });
            });
        });

        // ... tests here
    });
});</pre>

I start out by configuring the base URL for the file that will be under test and the path for Squire. Like the AngularJS example, the first real step is defining the mock service and it&#8217;s expected response. Before each test, I then use Squire to mock the ListOfItemsService and load a fresh copy of the Model I am putting under test to ensure each test starts with a clean slate.

Like before, the tests themselves are pretty straightforward at that point:

<pre>it("should start with an empty list of items", function (done) {
    expect(viewmodel.listOfItems()).toEqual([]);
});

it("should populate list from service when fillItems() is called", function (done) {
    viewmodel.fillItems();
    expect(viewmodel.listOfItems()).toEqual(expectedServiceResponse);
});</pre>

With the exception of having a viewmodel variable instead of a $scope variable and evaluating the listOfItems value with ()&#8217;s, the tests are almost identical to AngularJS&#8217;s.

As I mentioned at the beginning, the documentation was sparse, so for once the Knockout/RequireJS side of things took more twiddling and frustration to get working. Now that I do have the tests working, though, I think it&#8217;s given me enough of a grasp of the mechanism that I could handle more complex test cases just as easily as I felt I could with AngularJS.

## Some Differences

There were a number of frustrations before I got this working.

### Injecting Dependencies

Again, kudos to the AngularJS team for making testing such a major focus. Unfortunately, I&#8217;m still parsing and re-parsing the inject() documentation to try and understand how what I did above works. I think part of the problem is that it&#8217;s a little recursive, as the injector is being used and replaced all at once&#8230;or something. Basically I&#8217;m a monkey with a lighter at this point, I know how to make the fire come out but have no idea why or how it does so.

And then we get to RequireJS, which has no focus on unit testing and the couple libraries that have tied into it have very few blogs posts and little documentation behind them. Once I got this first example together, I am feeling much more confidant about doing it again. Unlike Angular&#8217;s inject method, I think I have a pretty good idea how Squire works, but it was still frustrating to get that first test worked out.

### Documentation

Usually AngularJS is the one that leaves me confused and forcing myself to reread the documentation. In this case, there are a lot of great posts out there that allowed me to ignore the documentation until I got it working (at which point I went back and started rereading it). Did I mention [Ben&#8217;s posts][12]?

Mocking modules for RequireJS was much more sparse. To the point where I started recognizing their page titles in google as I was searching alternative terms to try and get more information. There are a very small number of posts on Squire, a few on testr.js, etc. I think the new secret phrase for people looking into this should be &#8220;hurp durp example&#8221;. If you have spent any time looking into this topic, you not only know what I&#8217;m talking about, you&#8217;ve also read it like 40 times in case you missed something.

## Final Thoughts

The AngularJS side was faster to get up to speed on and required fewer external dependencies. The Knockout/RequireJS side required me to pull in Squire and Jasmine.async and was tougher to get running initially. But now that I have them both running, I&#8217;ve found I don&#8217;t have a strong opinion about either of them, neither looks like it will be that much harder to extend or build more tests with. 

I am definitely looking forward to playing with [Karma][7] in the near future. I am used to continuous test execution and coverage with [NCrunch][13] and going back to manually executing tests has been annoying.

<div style="background-color: #DDDDDD; padding: 8px; width: 400px;">
  <h3>
    Knockout vs AngularJS
  </h3>
  
  <ul style="margin: 0px; padding: 4px;">
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1">Introductory Post</a>
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
      <b>Automated Testing</b>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/AJAX/angularjs-vs-knockout-spa-routing-history-8">SPA Routing/History</a>
    </li>
    <li>
      <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-final-thoughts-9">Final, Final Thoughts</a>
    </li>
  </ul>
</div>

 [1]: https://github.com/tarwn/AngularJS-vs-Knockout/ "View all of the post examples on github"
 [2]: http://pivotal.github.io/jasmine/
 [3]: http://requirejs.org/
 [4]: https://github.com/iammerrick/Squire.js/
 [5]: https://github.com/derickbailey/jasmine.async
 [6]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/SpecRunner.html "View full example file on github"
 [7]: http://karma-runner.github.io/0.10/index.html
 [8]: /index.php/WebDev/UIDevelopment/angularjs-vs-knockout-modules-and-di-6
 [9]: http://www.benlesh.com/ "Benjamin Lesh: Try, Catch, Fail"
 [10]: https://twitter.com/BenLesh "@BenLesh on twitter"
 [11]: http://docs.angularjs.org/api/angular.mock.inject "AngularJS: angular.mock.inject"
 [12]: http://www.benlesh.com/2013/06/angular-js-unit-testing-services.html "Ben Lesh - Try, Catch, Fail - 'Angular JS - Unit Testing - Services'"
 [13]: /index.php/EnterpriseDev/UnitTest/reducing-code-build-test-friction "LessThanDot Blog - 
Reducing Code-Build-Test Friction with NCrunch"