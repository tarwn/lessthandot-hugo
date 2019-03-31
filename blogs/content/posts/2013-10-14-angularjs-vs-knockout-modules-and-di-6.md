---
title: AngularJS vs Knockout – Modules and DI (6 of 8)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-14T14:06:00+00:00
excerpt: "I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. The larger or more complex a project, the more important it is to be able to modularize the code. Modules provide organization, ensure script load&hellip;"
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-modules-and-di-6/
featured_image: /wp-content/uploads/2013/10/Javascript2.png
views:
  - 25565
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - angularjs
  - dependency injection
  - knockout
  - requirejs

---
I&#8217;m reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. The larger or more complex a project, the more important it is to be able to modularize the code. Modules provide organization, ensure script loading order is correct, and enable dependency injection for cleaner unit testing. AngularJS brings a built-in ability to define modules and inject dependencies. With Knockout, we&#8217;ll look at using [RequireJS][1], and AMD packages.

<div style="background-color: #eeeeee; padding: 1em;">
  This is the sixth of eight posts looking at the capabilities of knockout and Angular. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1" title="AngularJS vs Knockout - Introduction">introduction post</a>, I outlined the capabilities that I am evaluating for. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-templating-5" title="AngularJS vs Knockout - Templating">fifth post</a>, I looked at templating. This post explores defining modules and dependency injection.
</div>

All of the examples presented throughout the series are available in the [tarwn/AngularJS-vs-Knockout][2] repository on github.

## Modules in AngularJS

The ability to define [modules][3] is built into AngularJS, but it doesn&#8217;t include an asynchronous loading option, so when we get to that point we&#8217;ll be including the external library [script.js][4].

### AngularJS Simple Dependency Injection Example

Full source available at [Angular/SimpleDI.html][5].

Angular modules are collections of functionality with a shared configuration block. Controllers, services, and even values can be attached to the module and will be loaded with the module. Modules can have dependencies on one another, which affects their loading order. Controllers and Providers (services, factories, etc) list their dependencies when they are defined, and those dependencies are filled by Angulars injector.

Here is an example based on the earlier sample for data binding, except our controller has a dependency on the &#8220;sampleServices/ListOfItemsService&#8221; service and when we push the button it will call this service to obtain the list of items to be displayed.

<pre>&lt;html ng-app="sampleApp"&gt;
&lt;head&gt;
    &lt;!-- ... --&gt;
    &lt;script type="text/javascript" src="js/lib/angular-1.0.8.min.js"&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div ng-controller="ModuleDIController"&gt;
	
    Text Value: {{ textValue }}&lt;br /&gt;
    List Of Items: &lt;ul ng-repeat="item in listOfItems"&gt;
                        &lt;li&gt;{{ item.number }} - {{ item.name }}&lt;/li&gt;
                   &lt;/ul&gt;&lt;br /&gt;

    &lt;input type="button" ng-click="fillItems()" value="Call Service"/&gt;
&lt;/div&gt;</pre>

And the module, controller, and service:

<pre>var sampleServices = angular.module('sampleServices', []);
sampleServices.service('ListOfItemsService', function () {
    this.getList = function () {
        // pretend call
        return [
			{ name: "first value", number: 123 },
			{ name: "second value", number: 456 },
			{ name: "third value", number: 789 }
        ];
    };
})

// and a module that has a controller that depends on the ListOfItemsService
var sampleApp = angular.module('sampleApp', ['sampleServices']);
sampleApp.controller('ModuleDIController',
    ['$scope', 'ListOfItemsService',
    function ($scope, listOfItemsService) {
        $scope.textValue = "some text";
        $scope.listOfItems = [];

        $scope.fillItems = function () {
            $scope.listOfItems = listOfItemsService.getList();
        };
    }]
);</pre>

As part of the example I&#8217;ve listed both the dependencies ($scope and ListOfItemsService) and then defined the controller function to take these two properties. Angular has the ability to infer dependencies based on the name of the parameters or list them explicitly like this. The explicit method is safe for a wider range of minification programs, so I decided to try it out.

One advantage to working with modules like this is that we can define things like these services independently from the logic that is going to use them, then let the library (AngularJS in this case) figure out how to wire the pieces back together again. This takes a lot of complexity and extra work out of our hands, because we no longer have to deal with juggling a long list of includes or functions into the right order. It also keeps the root namespace clear and makes it simpler to see what external dependencies a chunk of code is using. We can also swap out the provider, replacing it with one that has different functionality, caching, or stubs it out for testing purposes.

### AngularJS Errors in Modules

Full source available at [Angular/ModuleError.html][6].

The next thing I&#8217;m concerned with is how errors will look once the framework has wired together my dependencies. Instead of returning a list of items, I&#8217;m going to modify the service to throw an explicit error and see what the result looks like.

<pre>var sampleServices = angular.module('sampleServices', []);
sampleServices.service('ListOfItemsService', function () {
    this.getList = function () {
        throw new Error("Error occurred, do we know where?");
    };
})</pre>

In Chrome I get a clean stack trace with clickable references to the files and correct line numbers, Firefox gets the same information but it&#8217;s jumbled with extra characters (possibly intended specifically for chrome output?) and the whole error is a single link that simply expands to show some properties that aren&#8217;t very useful. This is less than great for an unexpected error during development, but it also means business as usual for expected production errors, as they&#8217;ll be able to use some standard error handling code instead of something new.

### AngularJS Module Loading

Full source available at [Angular/AsyncDI.html][7].

By default, Angular does not include a method for loading scripts. The documentation for [module][8] indicates that there are external libraries that solve this, but don&#8217;t indicate what they are. Searching for something I could use as an example, I ran into a post (and didn&#8217;t copy down the URL, sorry) that uses require.js and a mention by someone that they had used [script.js][4]. I also ran into a post ([Building Huuuuuge Apps with AngularJS][9]) by one of the core Angular team members, Brian Ford, that recommends against using RequireJS with Angular, so I suppose script.js it is.

Using script.js, I remove the ng-app attribute and instead bootstrap the document to use the sampleApp module once the two files are loaded.

<pre>&lt;html&gt;
&lt;head&gt;
    &lt;!-- ... --&gt;
    &lt;script type="text/javascript" src="js/lib/angular-1.0.8.min.js"&gt;&lt;/script&gt;
    &lt;script type="text/javascript" src="js/lib/script.js"&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div ng-controller="ModuleDIController"&gt;
	
    Text Value: {{ textValue }}&lt;br /&gt;
    List Of Items: &lt;ul ng-repeat="item in listOfItems"&gt;
                        &lt;li&gt;{{ item.number }} - {{ item.name }}&lt;/li&gt;
                   &lt;/ul&gt;&lt;br /&gt;

    &lt;input type="button" ng-click="fillItems()" value="Call Service" /&gt;
&lt;/div&gt;</pre>

And the javascript section of the page is reduced to defining the path that the scripts should be loaded from, and the bootstrap code that replaces the ngApp directive.

<pre>$script.path('js/AsyncDI/');
$script(['sampleApp', 'sampleServices'], function () {
    angular.bootstrap(document, ['sampleApp']);
});</pre>

The downside of this method is that it requires me to list all of the files I want to load, which is easy when you have a couple files, but is going to be nasty when I start getting past about 10-20 files (and I imagine it will be real nasty if I have hundreds of files).

## Modules in Knockout

Once again we&#8217;re looking at a feature that is outside the scope of Knockout, so it&#8217;s time to turn to a 3rd party library. The most popular choice here seems to be [RequireJS][1].

### Knockout/RequireJS Simple Dependency Injection Example

Full source available at [Knockout/SimpleDI.html][10].

Using the simple databinding example as a base, here is an example of using RequireJS modules to define a module and a service in seperate modules, then using require to ensure the module with the viewmodel is loaded prior to instantiating and binding it:

<pre>&lt;html&gt;
&lt;head&gt;
	&lt;!-- ... --&gt;
	&lt;script type="text/javascript" src="js/lib/knockout-2.3.0.min.js"&gt;&lt;/script&gt;
	&lt;script type="text/javascript" src="js/lib/require-2.1.8.min.js"&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div&gt;
    Text Value: &lt;span data-bind="text: textValue"&gt;&lt;/span&gt;&lt;br /&gt;
    List Of Items: &lt;ul data-bind="foreach: listOfItems"&gt;
		                &lt;li data-bind="text: number + ' - ' + name"&gt;&lt;/li&gt;
                   &lt;/ul&gt;&lt;br /&gt;
    &lt;input type="button" data-bind="click: fillItems" value="Call Service"/&gt;
&lt;/div&gt;</pre>

And the javascript driving the form:

<pre>define("sampleServices/ListOfItemsService", function () {
    // return an object literal for the service object
    return {
        getList: function () {
            return [
                { name: "first value", number: 123 },
                { name: "second value", number: 456 },
                { name: "third value", number: 789 }
            ];
        }
    }
});

// and a module that has a controller that depends on the ListOfItemsService
define("sampleApp/ModuleDIModel",
    ["sampleServices/ListOfItemsService"],
    function (listOfItemsService) {
        // return a constructor for the model
        return function () {
            this.textValue = ko.observable("some text");
            this.listOfItems = ko.observableArray([]);

            this.fillItems = function () {
                this.listOfItems(listOfItemsService.getList());
            };
        }
    }
);

// use require to define dependencies to start and bind viewmodel
require(["sampleApp/ModuleDIModel"],
    function (ModuleDIModel) {
        var viewmodel = new ModuleDIModel();
        ko.applyBindings(viewmodel);
    }
);</pre>

Each define block has 3 arguments, the name of the module, the array of dependencies it requires, and the function it executes when initially resolved. The require block at the bottom then lists some requirements for inline execution of the included function and executes the function when they are available.

Like the Angular example, the service module will return a single instance of the ListOfItemsService that will be used by anyone needing it, while the ModuleDIModel returns a function constructor for the viewmodel. When I tell RequireJS I require the ModuleDIModel, it automatically resolves the dependency on the ListOfItemsService module.

### Knockout/RequireJS Errors in Modules

Full source available at [Knockout/ModuleError.html][11].

Once again, my next concern is whether troubleshooting errors will be impaired. Substituting an error for the return of the ListOfItemsService again:

<pre>define("sampleServices/ListOfItemsService", function () {
    return {
        getList: function () {
            throw new Error("Error occurred, do we know where?");
        }
    }
});</pre>

In Chrome, the console shows the error with a short stack trace which, when clicked, takes me directly to the line that produced the error. In Firefox, the console shows both the error message and the offending line, which opens up the source to the correct spot when clicked.

Again, this is primarily only going to be an issue during development, a I will hopefully have appropriate error handling logic in the application for expected errors in production (and I don&#8217;t expect my users to troubleshoot them for me).

### Knockout/RequireJS Module Loading

Full source available at [Knockout/AsyncDI.html][12].

RequireJS is built specifically for asynchronous module loading, so where we only have module level injection, instead of controller level in AngularJS, we do already have the asynchronous module loading of RequireJS. To switch from inline scripts to asynchronously loaded ones, all we have to do is make a few changes:

<pre>require.config({
    baseUrl: 'js/AsyncDI'
});

require(["sampleApp/ModuleDIModel"],
    function (ModuleDIModel) {
        var viewmodel = new ModuleDIModel();
        ko.applyBindings(viewmodel);
    }
);</pre>

Like the AngularJS exmaple, I define a base URL for the library and then relay on it to run my bootstrapping code. In this case the bootstrap code hasn&#8217;t changed, though. RequireJS now automatically looks for js/AsyncDI/sampleApp/ModuleDIModel.js and resolves it&#8217;s dependencies too, then runs my method.

## Some Differences

Where do I start? Most of this post was a comparison of external libraries, so while it was hopefully useful, I&#8217;m not going down a script.js vs RequireJS path (you can check this out instead: [$script.js vs RequireJS: Dependency Management Comparisons][13]).

In fact, there is really only one difference we can look at here:

### It isn&#8217;t built in

In the case of Knockout, we once again had to pull in an extra package. As always, this means we now have to track and manage yet another library that has separate security vulnerabilities, updates, and support team from the core library. It&#8217;s a tradeoff and you have to decide whether it&#8217;s worth it or not.

### It isn&#8217;t built in

In the case of Angular, Modules and dependency injection are built in, but they suggest either keeping everything in a few files or merging them with a DIY build process. In my book, 1000+ line files are a code smell and I can&#8217;t stand them, but I think what it comes down to at the end of the day is whether you are ok with big files, want to use an external library (like script.js or RequireJS), or want to merge and minify them in your build process (you do have a build process, right?).

I&#8217;d compare RequireJS + Knockout to Angular, but when we included RequireJS we not only got modules, we also got asynchronous loading, so then we had to ratchet Angular up a notch and add script.js to get back to the same spot.

## Final Thoughts

In both cases there is one unanswered issue. When is your application too big for keeping it all in a couple files? When does it become so large it impacts the user&#8217;s ability to load it via script tags, impacts our ability to keep our dependencies straight, etc. I think 10 &#8211; 20 modules is not too bad to keep track of as separate files, but I wouldn&#8217;t want to go larger than that. I could easily use namespacing with Knockout instead of a module loader for something that small, though it&#8217;s not as clean when it comes time to unit test (that post is coming, don&#8217;t worry).

So what is big? When do we need to switch from a few files or one big minified file to a module loader or a bundler like [Cassette][14] that can auto bundle everything based on include comments? Is there a sweet spot in between where just have modules and DI without asynchronous loading is perfect? Or is asynchronous loading for &#8220;huge&#8221; applications (defined by Brian Ford above as tens and hundreds of thousands of lines) totally unnecessary?

I don&#8217;t have those answers, but they are important considerations we should be making when we sit down and try to choose between AngularJS and Knockout on a future project.

I think the AngularJS DI system works really well, and I was really happy about how it performed for the Unit testing examples (next post). I&#8217;m not crazy about the inferred method for DI, I feel like it saves a little typing while limiting minification and potential asynchronous loading options.

When I first looked at RequireJS for defining modules (and at Angular&#8217;s method, to be honest), it looked messy. They both still look a bit messy, but after working through these examples, I&#8217;ve decided I do actually like it and I&#8217;m planning on retrofiting some past projects to use RequireJS. And, unlike the build your own file merger suggestion for Angular, it already has well documented tools and methods for [optimizing][15] via your build process.

So, not a strong conclusion or set of comparisons for this post, but I&#8217;m sure we can hash it out in the comments 🙂

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
      <b>Modules + Dependency Injection</b>
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

 [1]: http://requirejs.org/
 [2]: https://github.com/tarwn/AngularJS-vs-Knockout/ "View all of the post examples on github"
 [3]: http://docs.angularjs.org/guide/module "AngularJS: Modules"
 [4]: https://github.com/ded/script.js
 [5]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/SimpleDI.html "View full example file on github"
 [6]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/ModuleError.html "View full example file on github"
 [7]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/AsyncDI.html "View full example file on github"
 [8]: http://docs.angularjs.org/guide/module
 [9]: http://briantford.com/blog/huuuuuge-angular-apps.html
 [10]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/SimpleDI.html "View full example file on github"
 [11]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/ModuleError.html "View full example file on github"
 [12]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/AsyncDI.html "View full example file on github"
 [13]: http://www.joezimjs.com/javascript/script-js-vs-requirejs-dependency-management-comparisons/
 [14]: http://getcassette.net/ "Cassette | Asset bundling for .Net"
 [15]: http://requirejs.org/docs/optimization.html "RequireJS Optimizer"