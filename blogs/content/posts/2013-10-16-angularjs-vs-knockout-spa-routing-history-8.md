---
title: AngularJS vs Knockout – SPA Routing/History (8 of 9)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-16T12:29:00+00:00
ID: 2179
excerpt: |
  I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. Let's talk Single Page applications, and more specifically, Routing and History. While AngularJS doesn't have the word "SPA" on their front page, the tutorial jumps straight into building one. How hard is it to give Knockout the same routing capability? Does it end up worse?
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-spa-routing-history-8/
views:
  - 54613
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - angularjs
  - history
  - knockout
  - routing
  - spa

---
I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. Let's talk Single Page applications, and more specifically, Routing and History. While AngularJS doesn't have the word "SPA" on their front page, the tutorial jumps straight into building one. How hard is it to give Knockout the same routing capability? Does it end up worse? 

<div style="background-color: #eeeeee; padding: 1em;">
  This is the eighth of <del>eight</del> nine posts looking at the capabilities of knockout and Angular. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1" title="AngularJS vs Knockout - Introduction">introduction post</a>, I outlined the capabilities that I am evaluating for. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-automated-testing-7" title="AngularJS vs Knockout - Automated Testing">seventh post</a>, I looked at unit testing. This post explores routing and history.
</div>

All of the examples presented throughout the series are available in the [tarwn/AngularJS-vs-Knockout][1] repository on github.

## Routing in AngularJS

Routing is built into AngularJS and is one of the 12 steps of the [AngularJS][2] Tutorial. The [$route][3] provider is used to define routes and watching the URL bar to capture and apply changes.

### Routing in AngularJS

Full source available at [Angular/Routing.html][4].

A basic use case for routing would be to provide a listing page where each item links to it's own detail page. Our expectation would be that loading the page will show the list, that the detail pages will allow navigation back to the list page, and that putting a URL in for a details page directly will load that page properly.

For the example, we'll need a list controller, details controller, and a listing service:

```javascript
sampleApp.service('ListOfStuffService', function () {
    var stuff = [
        { id: 1, name: "First Item", description: "It's the first item, woohoo!" },
        { id: 2, name: "Second Item", description: "Numero Dos!" },
        { id: 3, name: "Third Item", description: "The third item, this one is always out of stock" }
    ];

    return {
        getAll: function () {
            return stuff;
        },
        getById: function (id) {
            for (var i in stuff) {
                if (stuff[i].id == id)
                    return stuff[i];
            }
        }
    };
});

sampleApp.controller('ListOfStuffController', ['$scope', 'ListOfStuffService', function ($scope, listService) {
    $scope.test = 5;
    $scope.listOfItems = listService.getAll();
    console.log($scope.listOfItems);
}]);

sampleApp.controller('StuffDetailController', ['$scope', '$routeParams', 'ListOfStuffService', function ($scope, routeParams, listService) {
    $scope.item = listService.getById(routeParams.id);
}]);

```
Implementing the routes is then pretty straightforward:

```javacript
sampleApp.config(['$routeProvider', function (routeProvider) {
    // unlike the documentation, I had to put quotes around my controllers - maybe they had global variables?

    routeProvider.when('/ListOfStuff', { templateUrl: 'partials/Routing/ListOfStuff.html', controller: 'ListOfStuffController' });

    routeProvider.when('/StuffDetail/:id', { templateUrl: 'partials/Routing/StuffDetail.html', controller: 'StuffDetailController' });

    routeProvider.otherwise({ redirectTo: '/ListOfStuff' });
}]);
```
To translate, the URL <code class="codespan">routing.html#/ListOfStuff</code> will show us the ListOfStuff.html template, the URL <code class="codespan">routing.html#/StuffDetail/123</code> will show us the StuffDetail template and pass along a parameter named "id" with 123 in it, and if no route matches, redirect to the first one.

The HTML templates then look like this:

[Angular/partials/Routing/ListOfStuff.html][5]

```html
<div>
    <ul ng-repeat="item in listOfItems">
        <li><a href="#/StuffDetail/{{ item.id }}">{{ item.name }}</a></li>
    </ul>
</div>
```
[Angular/partials/Routing/StuffDetail.html][6]

```html
<div>
    <a href="#/ListOfStuff">Back to list</a><br />
    <br />
    <b>Id:</b> {{ item.id }}<br />
    <b>Name:</b> {{ item.name }}<br />
    <b>Description:</b> {{ item.description }}<br />
</div>
```
In contrast to some of the other AngularJS examples in earlier posts, this one just worked. I didn't have to worry about how to make hash URLs work, detecting changes and writing code to parse the URLs, or anything, just a few simple, direct rules and a controller and template for each one. 

## Routing in ... Knockout?

Well, crap. Up until now there has been some clear, obvious answers when I needed to pull in an extra library for Knockout. When it comes to routing and SPA-like behavior, the two answers I see the most frequently are [Durandal][7] (see Avi, I spelled it right that time) and [Sammy.js][8]. Knockout has dynamic template bindings, so for this post maybe Sammy will be enough? And why get locked in, let's pick a few others at random and do them too. So here we go.

In all of these examples, we will need a ListOfStuffService, a ListOfStuffViewModel, a StuffDetailViewModel, and an overall viewmodel to represent the page and have these viewmodels (and their associated template) assigned to it. To reduce the amount of necessary example code, I've assigned the template names to the viewmodels.

```javascript
// basic fake service with a GetAll and GetById call
define("ListOfStuffService", function () {
    var stuff = [
                { id: 1, name: "First Item", description: "It's the first item, woohoo!" },
                { id: 2, name: "Second Item", description: "Numero Dos!" },
                { id: 3, name: "Third Item", description: "The third item, this one is always out of stock" }
    ];

    return {
        getAll: function () {
            return stuff;
        },
        getById: function (id) {
            for (var i in stuff) {
                if (stuff[i].id == id)
                    return stuff[i];
            }
        }
    };
});

// and a module that has a controller that depends on the ListOfItemsService
define("ko-app",
    ["knockout"],
    function (ko) {
        return function () {
            this.viewmodel = ko.observable();
        }
    }
);

// and a module that has a controller that depends on the ListOfItemsService
define("ListOfStuffViewModel",
    ["knockout", "ListOfStuffService"],
    function (ko, listOfItemsService) {
        return function () {
            this.template = "ListOfStuffViewModel";
            var items = listOfItemsService.getAll();
            this.listOfItems = ko.observableArray(items);
        }
    }
);

// and a module that has a controller that depends on the ListOfItemsService
define("StuffDetailViewModel",
    ["knockout", "ListOfStuffService"],
    function (ko, listOfItemsService) {
        return function (id) {
            this.template = "StuffDetailViewModel";
            var item = listOfItemsService.getById(id);
            this.item = ko.observable(item);
        }
    }
);
```
And the HTML for the page and the viewmodels looks like this:

```html
<body>
<!-- ko if: viewmodel() -->
<div data-bind="template: { name: viewmodel().template, data: viewmodel }">
</div>
<!-- /ko -->

<!-- ... -->

<script type="text/html" id="ListOfStuffViewModel">
    <ul data-bind="foreach: listOfItems">
        <li><a data-bind="attr: { href: '#/StuffDetail/' + id }, text: name"></a></li>
    </ul>
</script>
<script type="text/html" id="StuffDetailViewModel">
    <div>
        <a href="#/ListOfStuff">Back to list</a><br />
        <br />
        <b>Id:</b> <span data-bind="text: item().id"></span><br />
        <b>Name:</b> <span data-bind="text: item().name"></span><br />
        <b>Description:</b> <span data-bind="text: item().description"></span><br />
    </div>
</script>
</body>
```
So far, the only major addition over the Angular example above is that outer viewmodel and the HTML to conditionally render it above. That "if" binding means that Knockout will not evaluate/display that area's contents when the bound value is falsey. 

I've made the examples slightly more complex than they needed to be because I'm still using [RequireJS][9] throughout them. This wasn't necessary and probably serves as a little extra noise, but oh well.

### Knockout Routing w/ Sammy.js

Full source available at [Knockout/Routing.html][10].

So we have our viewmodels, we have a bare app viewmodel they will get socketed into, and we have HTML templates. Let's define the routes using the [Sammy.js][8] library:

```javascript
// define route and outer ko viewmodel
require(['knockout', 'ko-app', 'sammy'], function (ko, AppViewModel, sammy) {
    var app = new AppViewModel();
    ko.applyBindings(app);

    var routing = sammy(function () {

        this.get('#/ListOfStuff', function (context) {
            require(['ListOfStuffViewModel'], function (ViewModel) {
                app.viewmodel(new ViewModel());
            });
        });

        this.get('#/StuffDetail/:id', function (context) {
            var id = this.params['id'];
            require(['StuffDetailViewModel'], function (ViewModel) {
                app.viewmodel(new ViewModel(id));
            });
        });

        var self = this;
        this.get('', function (context) {
            self.runRoute('get', '#/ListOfStuff');
        });

    });
    routing.run();
});
```
Using the Sammy library, I've defined the same three cases I had in Angular. Each route has a callback that it calls when the route is matched, which I used to create the appropriate ViewModel and assign it to my main "app" viewmodel, causing the template to be changed. If the empty route is matched, I explicitly run the ListOfStuff route.

### Knockout Routing w/ Finch.js

Full source available at [Knockout/Routing.FinchJS.html][11].

The tagline for [FinchJS][12] is "Powerfully Simple Javascript Routing", and I have to agree that the library meets expectations. Implementing the same routes as the last two exmaples:

```javascript
// define route and outer ko viewmodel
require(['knockout', 'mainApp', 'finch'], function (ko, AppViewModel, finch) {
    var app = new AppViewModel();

    finch.route("/", function () {
        finch.call("/ListOfStuff");
    });

    finch.route("/ListOfStuff", function () {
        require(['ListOfStuffViewModel'], function (ViewModel) {
            app.viewmodel(new ViewModel());
        });
    });

    finch.route("/StuffDetail/:id", function (bindings) {
        require(['StuffDetailViewModel'], function (ViewModel) {
            app.viewmodel(new ViewModel(bindings.id));
        });
    });

    ko.applyBindings(app);
    finch.listen();
});
```
The route logic looks almost the same as Sammy.js, proving that I probably picked too easy of an example for this post. 

### Knockout Routing w/ flatiron director

Full source available at [Knockout/Routing.Director.html][13].

One of the key goals for the [flatiron director]() library is to work as seamlessly as possible in both the node.js and browser environments. director has HTML5 History API support, but I've configured it off for this example to match the others:

```javascript
require(['knockout', 'mainApp', 'director'], function (ko, AppViewModel, director) {
    var app = new AppViewModel();

    var routes = {
        '/ListOfStuff': function () {
                            require(['ListOfStuffViewModel'], function (ViewModel) {
                                app.viewmodel(new ViewModel());
                            })},
        '/StuffDetail/:id': function (id) {
                            require(['StuffDetailViewModel'], function (ViewModel) {
                                app.viewmodel(new ViewModel(id));
                            })}
    };

    ko.applyBindings(app);
    var router = director(routes);
    router.init('/ListOfStuff')
            .configure({
                html5history: false
            });
});
```
In this case, the routes are defined as an array and I only define the two real routes. Once I've applied my bindings, I start up the routing by calling init with the default URL to use if there isn't a hash address in the path.

This example does have an error, in that it doesn't actually work if I load a hashed address from scratch. All three other examples work fine, so I suspect it's something I've done wrong.

## Some Differences

Angular's injection kept the routing cleaner for that example, but really the biggest issue is that I didn't pick a complex enough example. All of the methods I tried were easy to implement. 

**Templating**

Angular's routing automatically uses templating, whereas the others are using Knockout and using the standard templating that is built into knockout. Although, like so much of the rest of this series, composing external templates onto the knockout side is just a library away, in this case the [Knockout.js External Template Engine][14].

I put together a copy of the Finch example above with external templating here: [Knockout/Routing.FinchJS.ExtTemplates.html][15]

**The Details**
  
As I mentioned above, all of these examples were really easy to put together, so I don't think I really exercised the libraries to show off their differences. That being said, which package you choose (I've had people suggest I use custom routing for Angular, even) is probably going to depend on those details and which flavor makes the most sense for your scenario. 

## Final Thoughts

Man that was easy.There are a ton of good routing libraries out there and it seems like even if you made the wrong decision when you picked one, it would be relatively easy to switch to a different one later. I personally liked Finch.js the best, but if I was using AngularJS I'd probably ride the default library as long as I could (and may never have a reason to change).

Using the dynamic template to change "pages" was also easy. I banged on the links a while and Chrome said I was using a lot of memory, but as soon as I let the GC clean up, it all cleared up. Banging on AngularJS also used memory, but it had GC's occurring along the way too, so I suspect if I were to use the knockout 'sorta-SPA' viewmodel in a production environment, I would actually want to use a method that delete()-ed the oold model as the new one was assigned.

## Post 8 of <del>8</del> 9

I'll be posting one last post for this series that is a round up of all the comparisons and feelings I had throughout the series, my opinion on which framework I would use, and a list of things I should have also looked at in the series. Keep an eye out on LessThanDot (and twitter, google plus, etc) for that post.

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

 [1]: https://github.com/tarwn/AngularJS-vs-Knockout/ "View all of the post examples on github"
 [2]: http://docs.angularjs.org/tutorial/step_07
 [3]: http://docs.angularjs.org/api/ngRoute.$route "AngularJS"
 [4]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/Routing.html "View full example file on github"
 [5]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/partials/Routing/ListOfStuff.html
 [6]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/partials/Routing/StuffDetail.html
 [7]: http://durandaljs.com/
 [8]: http://sammyjs.org/
 [9]: http://requirejs.org/
 [10]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/Routing.html "View full example file on github"
 [11]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/Routing.FinchJS.html "View full example file on github"
 [12]: http://stoodder.github.io/finchjs/
 [13]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/Routing.Director.html "View full example file on github"
 [14]: https://github.com/ifandelse/Knockout.js-External-Template-Engine
 [15]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/Routing.FinchJS.ExtTemplates.html "View full example file on github"