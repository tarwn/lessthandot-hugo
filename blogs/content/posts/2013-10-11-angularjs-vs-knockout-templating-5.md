---
title: AngularJS vs Knockout – Templating (5 of 8)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-10-11T12:56:00+00:00
excerpt: "I'm reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. A huge piece of both of these frameworks is the ability to create and reuse templates for the output. AngularJS brings template Directives, transc&hellip;"
url: /index.php/webdev/uidevelopment/angularjs-vs-knockout-templating-5/
views:
  - 21862
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - UI Development
tags:
  - angularjs
  - knockout

---
I&#8217;m reviewing Angular and Knockout to determine which would fit better for a variety of upcoming projects. A huge piece of both of these frameworks is the ability to create and reuse templates for the output. AngularJS brings template Directives, transclusion, and behavior to the table, while Knockout brings templating, dynamic template names, and it&#8217;s own ability to apply additional behavior.

<div style="background-color: #eeeeee; padding: 1em;">
  This is the fifth of eight posts looking at the capabilities of knockout and Angular. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-introduction-1" title="AngularJS vs Knockout - Introduction">introduction post</a>, I outlined the capabilities that I am evaluating for. In the <a href="/index.php/WebDev/UIDevelopment/angularjs-vs-knockout-serialization-4" title="AngularJS vs Knockout - Serialization">fourth post</a>, I looked at serialization. This post explores some of the templating capabilities in the two frameworks.
</div>

All of the examples presented throughout the series are available in the [tarwn/AngularJS-vs-Knockout][1] repository on github.

## Templating in AngularJS

[Directives][2] in Angular are touted as one of it&#8217;s &#8216;killer&#8217; features. They provide the ability to create entirely new elements using templates and custom behavior by replacing and manipulating DOM content. Angular Directives can be used to define custom behavior based on custom tags, attributes, and class names.

### AngularJS Simple Templating Example

Full source available at [Angular/SimpleTemplating.html][3].

Taking a username and turning it into a full twitter button seems like it could be a good case for templating. Creating a replacement element will move the actual code for the twitter button out of the flow, reducing repetition of the mess while making the original code just as readable (or even more so).

<pre><div ng-controller="TemplatingController"&gt;
    <h1&gt;As an Element</h1&gt;
    <twitter uservalue="{{ username }}"&gt;</twitter&gt;

    <!-- ... attribute example, see source ... --&gt;
</div&gt;
<script type="text/javascript"&gt;

// ...

sampleApp.directive('twitter', function(){
    return {
        // must be an element
        restrict: 'E',
        // replace it
        replace: true,
        // local scope will have a one-way binding to username from the referencing scope
        scope: {
            user: '@uservalue'
        },
        // just a basic div instead of pulling 
        template: '<iframe allowtransparency="true" frameborder="0" scrolling="no" src="//platform.twitter.com/widgets/follow_button.html?screen_name={{ user }}"  style="width:300px; height:20px;"&gt;</iframe&gt;'
    }
});

// ... 
</script&gt;</pre>

The element is straightforward and readable. The directive will replace the custom element, pulling the value of the uservalue attribute in and adding that to the template HTML that will produce a twitter button when displayed.

### AngularJS Templating with Transclusion

Full source available at [Angular/TransclusionTemplating.html][4].

Angular directives provide the ability to [transclude][5] content, inserting content from a custom element into the template in a directive. I&#8217;ve worked with a number of layouts over the years that have container elements, transclusion is a great tool to replace this repetitive (and occasionally fragile) code with a more readable and less repetitive element that provides the same final product. Here I&#8217;ve defined a basic Directive that for a container with a title and transcluded content.

<pre><div ng-controller="TransclusionTemplatingController"&gt;
    <!-- don't try to use myAwesomeContainer, it won't work --&gt;
    <my-awesome-container h1-title="{{ title }}"&gt;
        Here is my content: "{{ content }}"
    </my-awesome-container&gt;
</div&gt;
<script type="text/javascript"&gt;
    var sampleApp = angular.module('sampleApp', []);

    sampleApp.directive('myAwesomeContainer', function () {
        return {
            // must be an element
            restrict: 'E',
            // replace it
            replace: true,
            // use transclusion
            transclude: true,
            // local scope 
            scope: {
                titleValue: "@h1Title"
            },
            // pretending we have a fancy set of HTML for the container
            template: '<div class="fancy-pants"&gt;<h1&gt;{{ titleValue }}</h1&gt;<div ng-transclude&gt;</div&gt;</div&gt;'
        }
    });

    sampleApp.controller('TransclusionTemplatingController', function ($scope) {
        $scope.content = "Some Dynamic Content";
        $scope.title = "A Dynamic Title";
    });
</script&gt;</pre>

Defining dialogs, containers, and so on as standard templates could take a lot of repetitive code out of the source, provide a single touch point to modify the code for those containers, and reduce the size of the HTML file the end user is downloading. 

### AngularJS Templating with Behavior

Full source available at [Angular/BehaviorTemplating.html][6].

In the [validation post][7], I used the linking method of a Directive to build validation. With templates, we can use that same linking method to add behavior to the template. Taking the transcluded container one step further, I&#8217;m going to provide the ability to hide or show the content by clicking on the title of the container.

<pre><div ng-controller="BehaviorTemplatingController"&gt;
    <expanding-container clickable-title="{{ title }}"&gt;
        Here is my content: "{{ content }}"
    </expanding-container&gt;
</div&gt;
<script type="text/javascript"&gt;
    var sampleApp = angular.module('sampleApp', []);

    sampleApp.directive('expandingContainer', function () {
        return {
            // must be an element
            restrict: 'E',
            // replace it
            replace: true,
            // use transclusion
            transclude: true,
            // local scope 
            scope: {
                titleValue: "@clickableTitle"
            },
            // pretending we have a fancy set of HTML for the container
            template: '<div class="fancy-pants"&gt;<div&gt;{{ titleValue }}</div&gt;<div ng-transclude class="box-to-hide"&gt;</div&gt;</div&gt;',
            link: function (scope, element, attributes) {
                // copied and reformatted from doc examples here: http://docs.angularjs.org/guide/directive
                var opened = true;
                var title = angular.element(element.children()[0]);
                title.bind('click', toggle);

                function toggle() {
                    console.log("done");
                    opened = !opened;
                    element.removeClass(opened ? 'closed' : 'opened');
                    element.addClass(opened ? 'opened' : 'closed');
                };

                toggle();
            }
        }
    });

    sampleApp.controller('BehaviorTemplatingController', function ($scope) {
        $scope.content = "Some Dynamic Content";
        $scope.title = "Click title to toggle visibility";
    });
</script&gt;</pre>

Ten to eleven years ago, this would have been an ASP or PHP function that took content, wrapped it in some HTML, then slathered on some cross-browser javascript. The combination of transclusion and linking script is pretty powerful, providing an easy way to create dialog, container control, or panel logic in a single place with behavior, then re-use it in a readable format via custom tags.

I ran into a couple major speedbumps while working on this, however. The first was that I started out going down the complete wrong road. By default, the guide and documentation on the Angular site default to the latest unstable version, while I am using the latest stable version. This resulted in quite a bit of confusion, as I was originally using a function that doesn&#8217;t even exist in the stable version.

The other issue was the continued difficulty of debugging things that fail silently. While I was working on this, I often found myself staring at no results and no error message. It also took some fiddling to figure out which pattern to use when naming directives vs using them in the HTML. This is likely one of those things that you get used to as you use it heavily, but in the longer term could cause issues if you have to come back to it after to using it for a while or are bringing new developers up to speed with it for the first time.

## Templating in Knockout

[Templating][8] in Knockout is a binding, and it works and is defined similar to how the other bindings work. Templates are defined as sections of HTML and bound to a data model from the template binding.

### Knockout Simple Templating Example

Full source available at [Knockout/SimpleTemplating.html][9].

Just as I did in the AngularJS example above, I&#8217;m going to start with a simple template that turns a username into a twitter button.

<pre><div&gt;
    <h1&gt;Inside an Element</h1&gt;
    <div data-bind="template: { name: 'twitter-template', data: { user: username }}"&gt;</div&gt;
    <!-- ... --&gt;
</div&gt;
<script type="text/html" id="twitter-template"&gt;
    <iframe allowtransparency="true" frameborder="0" scrolling="no"
        data-bind="attr: { src: '//platform.twitter.com/widgets/follow_button.html?screen_name=' + user() }"
        style="width:300px; height:20px;"&gt;</iframe&gt;
</script&gt;</pre>

Templates in knockout fill the container they are defined on rather than replacing it. The data bind specifies the template name, which corresponds to a text/html block with the same id. The template binding specifies an anonymous object with the property as the viewmodel. This could as easily have been just the individual property, but I thought the anonymous object made it more similar to the Angular directive.

### Knockout Nested Templating Example

Full source available at [Knockout/NestedTemplating.html][10].

While knockout does not have the concept of tranclusion, it does have the ability to define templates dynamically using an observable or computed value for the template name. So, while knockout does not have a built-in ability to do transclusion, you can very easily pass along a sub-template name and viewmodel to render a nested template.

<pre><div data-bind="template: { name: 'container-template', data: { title: title, subtemplate: 'sample-contents', submodel: { content: content }}}"&gt;</div&gt;

<script type="text/html" id="container-template"&gt;
    <div class="fancy-pants"&gt;
        <h1 data-bind="text: title"&gt;</h1&gt;
        <div data-bind="template: { name: subtemplate, data: submodel}"&gt;</div&gt;
    </div&gt;
</script&gt;
   
<script type="text/html" id="sample-contents"&gt;
    <div data-bind="text: content"&gt;        
    </div&gt;
</script&gt;

<script type="text/javascript"&gt;
var SimpleTemplatingModel = function () {
    this.title = ko.observable("A Dynamic Title");
    this.content = ko.observable("Some Dynamic Content");
};

// ...</pre>

Nesting templates dynamically is probably a less frequent use case than transclusion, and not something you would run into anywhere near as often as good uses for Angular&#8217;s transclusion. But it does provide a neat example for dynamic templates, which have a much wider use case. You can easily build a SPA container using a high level template binding to an observable, then swap the value to new template names on demand (via routing).

### Knockout Templating w/ Behavior Example

Full source available at [Knockout/BehaviorTemplating.html][11].

Like Angular, knockout templates provide a mechanism to add behavior when they are applied. The template binding in knockout has [additional properties][12] that can be run as post-processing steps.

<pre><div data-bind="template: { name: 'container-template', data: { title: title, content: content }, afterRender: makeItToggly }"&gt;</div&gt;

<script type="text/html" id="container-template"&gt;
    <div class="fancy-pants"&gt;
        <h1 data-bind="text: title"&gt;</h1&gt;
        <div data-bind="text: content"&gt;</div&gt;
    </div&gt;
</script&gt;
<script type="text/javascript"&gt;
    var SimpleTemplatingModel = function () {
        this.title = ko.observable("A Dynamic Title");
        this.content = ko.observable("Click title to toggle visibility");
    };

    var viewmodel = new SimpleTemplatingModel();
    ko.applyBindings(viewmodel)

    // some jQuery behavior stuff
    function makeItToggly(elem) {
        $(elem).click(function () {
            $(elem).find('div').toggle();
        });
    }
</script&gt;</pre>

For this example, I&#8217;ve returned to the container that hides it&#8217;s content when the title is clicked. Knockout does not include DOM manipulation methods, so I&#8217;ve reached out to [jQuery][13] for the click handling and toggling (though if the library size worries you, I could have also gone to [Zepto.js][14], a lighter weight alternative).

Unlike the Angular example, there weren&#8217;t any major struggles to make this work.

## Some Differences

With templating, there are quite a few differences between the two frameworks, but I don&#8217;t think that would prevent you from implementing the same site in either.

**Readability**

Looking at an AngularJS custom element vs a Knockout binding on an HTML element, the AngularJS one is definitely a little cleaner and easier to read. In my examples above, I could have trimmed down the Knockout template bindings a little more, but it still would have been more cluttered than a single named element, like AngularJS.

**Dynamic Templating**

This is a great feature in Knockout, and I could see a number of uses for it when you have a site with several pages, multiple sections in a page, sections that transform between read and edit mode, etc. I did try to implement something like this in Angular using Directives and defining them in a couple different ways from changeable properties in the scope, but was not able to get it working.

 <span class="MT_orange">(Update)</span>

When I originally published this post, my AngularJS focus was solely on custom directives, but it turns out () that there is a built-in [ngInclude][15] binding that will include a template similarly to knockout&#8217;s dynamic template. It includes an external HTML file with a child scope that is derived from the current one. It also has an onload attribute, which is evaluated when the template is loaded.

Example: [Angular/ngIncludeTemplating.html][16]

It&#8217;s not a huge difference, but I like Knockout&#8217;s ability to specify the data model. With AngularJS, your scope is available to the template and it figures out what it wants to grab and manipulate, with knockout you can pass it an instance of exactly the viewmodel it&#8217;s expecting and the template doesn&#8217;t have to have any knowledge about the higher level viewmodel. It also differentiates between collections, which it will iterate over, and non-collections, which it will render one template against. On the plus side for Angular, it&#8217;s templates for ngInclude are separate files already, something you have to add a plugin to knockout to achieve.

**Transclusion**

Transclusion is an excellent tool with a wide set of use cases. I&#8217;d love to see a library combine transclusion and dynamic templates, as I think that these are easily one of the biggest differentiators for templating in the two libraries.

**Behavior**

With the Knockout example I pulled in a third party library (jQuery), and this comes with all the potential maintenance issues that you get when you start adding additional libraries. I don&#8217;t think the size is much of an issue, as I could just as easily have pulled in a smaller library. The other thing I didn&#8217;t like about Knockout was defining the behavior as a binding on the element rather than as part of the template. In my example above, I wanted to build a component with some behavior, but I had to bind that extra behavior from outside the template definition. Beside feeling a little hinky, it also could cause some problems if I was using dynamic templates with different behavior.

The AngularJS example kept the behavior together with the template definition, which I liked, but jqLite doesn&#8217;t have as extensive a set of functionality as a full standalone library like jQuery. If I needed that additional functionality, I don&#8217;t think I can remove jqLite from the file size of AngularJS, so I&#8217;m carrying a little extra baggage.

**Pain Points**

I feel like I have to point out again the pain points I ran into with AngularJS and it&#8217;s documentation. Even knowing that google is dropping me on the unstable branch and that the version is visible in the top left, in many cases it is very hard to go from a page in the unstable version to the corresponding page in a stable version, or to figure out what version a function was added so I can look for alternatives if it&#8217;s not in stable yet. This caused a great deal of confusion. As far as I can tell, Knockout&#8217;s documentation speaks to the latest stable/release version, and not the 3.0 version (beta when I started this, RC now). I do know that I haven&#8217;t had any struggles with it, where I seem to struggle frequently with the Angular side (maybe they assume everyone is using the unstable version or has long enough deployment cycles that it will be stable by the time we deploy?).

## Final Words

If we consider the end goal of using frameworks like these for templating, I feel that both bring a great deal of capability to the table. There are definitely differences in how they work and I felt those as I was working on the examples. I&#8217;d love to somehow have the transclusion ability from AngularJS, dynamic templating from Knockout, and combined behavior and template in a single place from AngularJS.

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
      <b>Templating</b>
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
 [2]: http://docs.angularjs.org/guide/directive "AngularJS: Directives"
 [3]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/SimpleTemplating.html "View full example file on github"
 [4]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/TransclusionTemplating.html "View full example file on github"
 [5]: http://docs.angularjs.org/api/ng.directive:ngTransclude "AngularJS: ngTransclude directive"
 [6]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/BehaviorTemplating.html "View full example file on github"
 [7]: /index.php/WebDev/UIDevelopment/angularjs-vs-knockout-validation-3 "AngularJS vs Knockout - Validation"
 [8]: http://knockoutjs.com/documentation/template-binding.html "Knockout: 'template' binding"
 [9]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/SimpleTemplating.html "View full example file on github"
 [10]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/NestedTemplating.html "View full example file on github"
 [11]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Knockout/BehaviorTemplating.html "View full example file on github"
 [12]: http://knockoutjs.com/documentation/template-binding.html#note_4_using_afterrender_afteradd_and_beforeremove "Knockout: The "template" binding"
 [13]: http://jquery.com/
 [14]: http://zeptojs.com/
 [15]: http://code.angularjs.org/1.0.8/docs/api/ng.directive:ngInclude "AngularJS: ngInclude"
 [16]: https://github.com/tarwn/AngularJS-vs-Knockout/blob/master/Angular/ngIncludeTemplating.html "View full example file on github"