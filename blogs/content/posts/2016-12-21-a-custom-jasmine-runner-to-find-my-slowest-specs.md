---
title: A Custom Jasmine Runner to find my slowest Specs
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-12-21T13:01:04+00:00
url: /index.php/webdev/a-custom-jasmine-runner-to-find-my-slowest-specs/
views:
  - 2856
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - jasmine
  - performance
  - phantomjs

---
I&#8217;ve been playing around lately with a pure command-line Jasmine runner that doesn&#8217;t rely on a SpecRunner file to run tests. I work daily with a largish application that is well over 100K lines of front-end code and greater than 7000 front-end tests. Over time as the codebase and test count has grown, our Continuous Integration environment has continued to get slower. While build servers like Jenkins and TeamCity provide some analytics around slow tests, there is still some digging involved to identify the best targets for improvement, something I&#8217;m hoping a local runner can make easier.

Sample code for this post: [tools/jasmine2-runner/jasmine2-runner.js][1] (embedded in a sample project named &#8220;townthing&#8221;).

I&#8217;ve taken a very small project I&#8217;ve used in prior posts on [Karma][2] and [WallabyJS][3] and written a reusable Jasmine console runner, relying on Phantom 2, that creates a set of statistics as it runs and tries to identify the slowest set of tests in the set without pushing it to a remote server.

## What are my slowest tests?

My test project is small enough that I won&#8217;t learn that much new, but it&#8217;s big enough to serve as an example.

I have Phantom installed locally and in my path, so to run the tests I can do this:

`phantomjs.exe .\tools\jasmine2-runner\jasmine2-runner.js "['../test/compass.spec', '../test/tile.spec', '../test/tree.spec', '../test/weather.spec']"`

The sample code uses requirejs, so I&#8217;m passing in an array of specs that will be used in a define statement prior to running jasmine.

The results from running this look like:

<pre>jasmine started
suiteDone [0.003s,10/10] : compass
suiteDone [0.175s,19/19] : tile
suiteDone [0.003s,8/8] : tree
suiteDone [0.007s,31/31] : weather
68/68 specs in 0.203s
-----------------------------------------------------------------
68 tests passed in 0.186s
Average Time: 0.003s
Standard Deviation: 0.004s
28% (19) of the tests account for 90% of the overall time.
15% (10) of the tests account for 50% of the overall time.
-----------------------------------------------------------------
Slowest Tests:
 [    0.014s]: tile -&gt; getEvaporationAmount -&gt; should be 0 if there are no trees and the terrain doesn't have dry evaporation
 [    0.010s]: tile -&gt; getEvaporationAmount -&gt; should be the terrain's evaporation if there are no trees
 [    0.010s]: tile -&gt; canSupportAdditionalTrees -&gt; should support additional trees if there is enough average rainfall for grass, existing trees, and a new tree
 [    0.009s]: tile -&gt; onGrow -&gt; should provide full amount of water to trees if available after watering the terrain
 [    0.009s]: tile -&gt; canSupportDryGrass -&gt; should be able to support dry grass when there is enough averageRainfall available
 [    0.009s]: tile -&gt; canSupportGrass -&gt; should be able to support grass when there is enough averageRainfall available
 [    0.009s]: tile -&gt; getPlantConsumptionAmount -&gt; should be 0 when the terrain doesn't require any water and there are no trees
 [    0.009s]: tile -&gt; canSupportAdditionalTrees -&gt; should not support a tree if there is not enough average rainfall for grass and new tree
 [    0.009s]: tile -&gt; onGrow -&gt; should evenly split remainder of water if there is not enough left after watering the terrain</pre>

So from the top:

  * I show the top suite names, so I have feedback on larger codebases
  * I get a X/Y specs in Z time overview, to help me see how long the test run took and how much was successful
  * Stats: general statistics on just the passing tests and the number of tests responsible for 50% and 90% of the runtime
  * Spec list: the tests responsible for 50% of the runtime, by runtime descending

There are several things we learn from this run:

  * A small number of tests (15%) account for half of the overall time (Pareto Principle<a />) </li> 
    
      * All of those 15% belong to the same top-level suite
      * There is ~.017s between the total from the specs and the overall run time</ul> 
    
    I don&#8217;t know if that ~0.017 is normal, but I&#8217;ve seen some fairly large numbers sneak our of other codebases where beforeEach logic was set at entirely too high a level, code was running outside the specs, and so on and in this case it&#8217;s low enough I wouldn&#8217;t focus on it. My first stop would be seeing what is going on with the tile class and suite, since that feels like more of a systematic issue across the whole suite than an individual test issue.
    
    ## How it works (and how to re-use it)
    
    This runner is not ready for drop-in use with another project, but it&#8217;s also not that far off. 
    
    In a nutshell, the script opens a Phantom page with minimal HTML and no URL. It then injects in jasmine, [a jasmine bootloader for RequireJS][4], a custom console runner, requireJS, a requireJS configuration, and then a script that require()&#8217;s the passed in spec list before running window.execute to run the tests. 
    
    The custom console runner takes care of capturing results from the tests and passes them back via the console log, which is captured in the outer phantom script for processing. The top-level suite output flows out as each suite is finished, but the stats wait until the full suite has run to minimize delays that show up if you interact with the console too heavily/frequently.
    
    Here is that runner:
    
    <pre>var Runner = {
    execute: function(callback){

        var page = require('webpage').create();

        page.onConsoleMessage = function(msg) {
            // … handle console output coming back from console worker
        };

        // build some fake content instead of using a real file/URL
        var expectedContent = '&lt;html&gt;&lt;head&gt;&lt;/head&gt;&lt;body&gt;&lt;/body&gt;&lt;/html&gt;';
        var expectedLocation = 'file:///' + fs.workingDirectory + '/';
        page.setContent(expectedContent, expectedLocation);

        // standard files
        page.injectJs('town/js/lib/jasmine-2.0.0/jasmine.js');
        page.injectJs('town/js/lib/jasmine-2.0.0/jasmine-html.js');
        page.injectJs('jasmine2-runner-boot.js');

        // inject reporter
        page.injectJs('console_reporter.js');
        page.evaluate(function(){
            jasmine.addReporter(new jasmineReporters.ConsoleReporter());
        });

        // inject additional required files
        page.injectJs('town/js/lib/require-2.1.11.js');

        // execute provided spec list
        page.evaluate(function(specs){

            // project's requirejs config for tests
            require.config({
                baseUrl: "town/js/src",
                paths: {
                    "knockout": "../lib/knockout-3.0.0",
                    "Squire": "../lib/Squire"
                }
            });

            require(specs, function(){
                window.executeTests();
            });

        }, files);
    }
};</pre>
    
    The statistics form the console runner are passed to a Processor class that flattens out the suite and spec hierarchy, sorts them by execution time, and then calculates the statistics we saw above.
    
    <pre>var StatsProcessor = {
    evaluate: function(rawStats){
        var flatStats = [];
        var flatSuites = [];
        StatsProcessor.flattenSpecs("", rawStats, flatStats, flatSuites);
        var sortedFlatStats = flatStats.sort(function(a,b){
            // sort descending by execution time
            return b.executionTime - a.executionTime;
        });

        var averages = StatsProcessor.getAverages(flatStats);
        if(averages.total &gt; 0){
            var fiftyPercentOfTotalIndex = StatsProcessor.getPercentOfTotalIndex(sortedFlatStats, averages, 0.50);
            var ninetyPercentOfTotalIndex = StatsProcessor.getPercentOfTotalIndex(sortedFlatStats, averages, 0.90);

            return {
                averageExecutionTime: averages.average,
                standardDeviation: averages.standardDeviation,
                totalExecutionTime: averages.total,
                totalCount: sortedFlatStats.length,

                fiftyPercent: {
                    numberOfTests: fiftyPercentOfTotalIndex + 1
                },
                ninetyPercent: {
                    numberOfTests: ninetyPercentOfTotalIndex + 1
                },

                specs: sortedFlatStats          
            };
        }
        else{
            return 0;
        }
    },

    flattenSpecs: function(description, stats, flatStats, flatSuites){
        // … work …
    },

    getAverages: function(flatStats){
        // … calculate avg and stddev …
    },

    getPercentOfTotalIndex: function(sortedFlatStats, averages, percentage){
        // … find tests that are responsiblce for _percentage_ of execution time …
    }
};</pre>
    
    Finally, we glue the two together in a simple statement:
    
    <pre>Runner.execute(function(result){
    var stats = StatsProcessor.evaluate(result);

    /* … display stats output … */

    phantom.exit();
});</pre>
    
    Customizing this for other projects is relatively easy, and I&#8217;ll probably work on making it easier to reuse as a I have more time. Right now the main things you need to do are:
    
      * Replace the jasmine paths with ones that make sense for your project
      * Replace the &#8220;inject additional required files&#8221; section with the additional dependencies you need
      * Update the &#8220;execute provided spec list&#8221; section to match how you run tests
    
    You will also want to download the runner, console-reporter, and jasmine bootloader from [github][5].
    
    For instance, if you are using a basic SpecRunner.html file with the spec and source files listed in script tags, you could drop these in the &#8220;inject additional required files&#8221; section and replace the &#8220;execute provided spec list&#8221; with just a single call to &#8220;windows.executeTests()&#8221;.

 [1]: https://github.com/tarwn/townthing/blob/master/tools/jasmine2-runner/jasmine2-runner.js
 [2]: /index.php/webdev/uidevelopment/javascript/continuous-javascript-testing-with-karma/ "Continuous Javascript Testing with Karma "
 [3]: /index.php/webdev/continuous-javascript-test-execution-with-wallabyjs/ "Continuous Javascript Test Execution with WallabyJS"
 [4]: /index.php/webdev/uidevelopment/javascript/unit-testing-with-jasmine-2-0-and-require-js/ "Unit Testing with Jasmine 2.0 and Require.JS"
 [5]: https://github.com/tarwn/townthing/tree/master/tools/jasmine2-runner "jasmine runner folder in github/tarwn/townthing"