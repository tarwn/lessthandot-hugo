---
title: Automatic Promise Resolution for AngularJS Unit Testing
author: Alex Ullrich
type: post
date: 2016-02-18T15:39:02+00:00
ID: 4359
url: /index.php/webdev/uidevelopment/javascript/automatic-promise-resolution-for-angularjs-unit-testing/
views:
  - 3064
rp4wp_auto_linked:
  - 1
categories:
  - Javascript

---
We've been using Angular for client-side apps for a couple years now, and it has mostly been great. The data binding works well, and the baked in dependency injection makes it very easy for our team of C# developers to write unit tests. There have been some painful moments, but they've mostly been growing pains on our part. One that has been particularly sticky was testing with mocked dependencies that return promises.

## Standard Approach

For us, the first time we were introduced to this stuff was when trying to test controllers using mocks of services that communicate over HTTP. Angular's [$http service][1] works asynchronously and returns promises, so by extension our services do as well. Luckily there is a lot of information out there to help with testing this kind of method, and everything we found pointed us to the [$q service][2]. This service is there for working with deferred objects, and is mostly pretty easy to get on with. Here is an example test from that page:

```javascript
it('should simulate promise', inject(function($q, $rootScope) {
  var deferred = $q.defer();
  var promise = deferred.promise;
  var resolvedValue;

  promise.then(function(value) { resolvedValue = value; });
  expect(resolvedValue).toBeUndefined();

  // Simulate resolving of promise
  deferred.resolve(123);
  // Note that the 'then' function does not get called synchronously.
  // This is because we want the promise API to always be async, whether or not
  // it got called synchronously or asynchronously.
  expect(resolvedValue).toBeUndefined();

  // Propagate promise resolution to 'then' functions using $apply().
  $rootScope.$apply();
  expect(resolvedValue).toEqual(123);
}));
```
The main sticking point here is $rootScope.$apply(). We can also call $rootScope.$digest() – but that method's intent might be even less clear. The reason these need to be called is not immediately clear from the code, but answers can be found in Angular's [$scope documentation][3]. Particularly

> When an external event (such as a user action, timer or XHR) is received, the associated expression must be applied to the scope through the $apply() method so that all listeners are updated correctly. 

When running a full blown angular application, we don't need to worry about this – it happens in the background and we can get on with our lives. But testing is a different story. As an application gets larger you find mysterious calls to $rootScope.$apply() or $rootScope.$digest() (or both) littered throughout your tests, in seemingly arbitrary places. If you have multiple promises in play, you might have several in a single test. In many tests (possibly even “most” tests if you use the controllerAs style) this might be your only reason to capture the injected scopoe. There must be a better way.

## 'AutoQ' Approach

What we had our sights on was a way to wrap $q and $rootScope in a single object that would know to call apply after any resolution or rejection. We called it 'autoQ' and the name stuck. Here is what the example test above looks like when using it:

```javascript
it('should simulate promise', inject(function($q, $rootScope) {
    var deferred = autoQ($q, $rootScope).defer();
    var promise = deferred.promise;
    var resolvedValue;

    promise.then(function(value) { resolvedValue = value; });
    expect(resolvedValue).toBeUndefined();

    // Simulate resolving of promise
    deferred.resolve(123);
  
    // promise will be resolved immediately
    expect(resolvedValue).toEqual(123);
}));

function autoQ($q, $rootScope) {
    return {
        defer: function() {
            var deferred = $q.defer();

            deferred.resolve = function() {
                this.__proto__.resolve.apply(this, arguments);
                $rootScope.$apply();
            }

            deferred.reject = function() {
                this.__proto__.reject.apply(this, arguments);
                $rootScope.$apply();
            }

            deferred.notify = function() {
                this.__proto__.notify.apply(this, arguments);
                $rootScope.$apply();
            }
            
            return deferred;
        }
    }
}
```

So now we don't have to call $rootScope.$apply(), but we do have to call the autoQ setup method, which is not ideal.

## Making it useful

When trying to figure out how to make this available to our tests, a trick we had been using to test the config phase of our application came to mind. Essentially what you do is declare a module upstream from your tests, and you can initialize it to capture dependencies taken in during the config phase (or anywhere in the module initialization process really). That “anywhere in the module initialization process” is where this gets interesting for our current problem. Any dependencies mutated or introduced through this dummy module will be available **in their modified state** to modules instantiated downstream. We had primarily done this as a way to set up spies for methods called during the config phase of our application, but the possibilities are endless. What we ended up doing was using this dummy module approach to register an 'autoQ' service that is only available in tests.

```javascript
//need to call angular.module here, NOT the module method exposed by angular mocks
    angular.module('setupAutoQ', [])
            .service('autoQ', function ($q, $rootScope) {

                return {
                    defer: function () {
                        var deferred = $q.defer();

                        deferred.resolve = function () {
                            this.__proto__.resolve.apply(this, arguments);
                            $rootScope.$apply();
                        }

                        deferred.reject = function () {
                            this.__proto__.reject.apply(this, arguments);
                            $rootScope.$apply();
                        }
                        
                        deferred.notify = function() {
                            this.__proto__.notify.apply(this, arguments);
                            $rootScope.$apply();
                        }

                        return deferred;
                    }
                }
            });

    //this is the angular mocks module method - we need to run the module once it has been declared
    module('setupAutoQ');
```
The last piece of the puzzle was how to run this prior to each test execution. Luckily [this StackOverflow answer][4] showed us that with jasmine you can declare a beforeEach outside any describe blocks, and it will be treated as a global setup method that is run before each test.

```javascript
beforeEach(function () {
    angular.module('setupAutoQ', [])
            .service('autoQ', function ($q, $rootScope) {

                return {
                    defer: function () {
                        var deferred = $q.defer();

                        deferred.resolve = function () {
                            this.__proto__.resolve.apply(this, arguments);
                            $rootScope.$apply();
                        }

                        deferred.reject = function () {
                            this.__proto__.reject.apply(this, arguments);
                            $rootScope.$apply();
                        }

                        deferred.notify = function() {
                            this.__proto__.notify.apply(this, arguments);
                            $rootScope.$apply();
                        }

                        return deferred;
                    }
                }
            });

    module('setupAutoQ');
});
```
## Wrapping it up

So now, we have an “autoQ” service that is available for testing. So our example test now looks like this:

```javascript
it('should simulate promise', inject(function(autoQ) {
    var deferred = autoQ.defer();
    var promise = deferred.promise;
    var resolvedValue;

    promise.then(function(value) { resolvedValue = value; });
    expect(resolvedValue).toBeUndefined();

    // Simulate resolving of promise
    deferred.resolve(123);
  
    // promise will be resolved immediately
    expect(resolvedValue).toEqual(123);
}));
```
This will really improve the signal to noise ratio in our tests, and hopefully help new developers get up to speed more quickly. Another nice benefit of this approach is that it does not modify $q – or anything in our “live” module – at all. So if we want finer grained control over promise resolution we can simply consume $q and $rootScope in our tests and use them as before. I'm not sure we'll ever need to do this, but its good to know we have the option.

 [1]: https://docs.angularjs.org/api/ng/service/$http "$http service"
 [2]: https://docs.angularjs.org/api/ng/service/$q "$q"
 [3]: https://docs.angularjs.org/guide/scope "$scope"
 [4]: http://stackoverflow.com/a/25053685/794 "Global beforeEach in Jasmine"