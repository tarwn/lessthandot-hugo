---
title: Stop Manually Updating Your Jasmine SpecRunner
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-10-06T12:29:16+00:00
ID: 4674
url: /index.php/webdev/stop-manually-updating-your-jasmine-specrunner/
views:
  - 3091
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - Gulp
  - jasmine
  - requirejs

---
I've used a number of test frameworks and runners over the years, but my first club out of the bag is still running a SpecRunner file in the browser, with all of the dev tools and console output I'm used to from normal debugging sessions. The painful bit has always been manually keeping the SpecRunner file up to date and forgetting every 3rd or 4th file. Having the SpecRunner is valuable, manually context switching to catch it up over and over is not. So let's automate it away.

In a prior post, I used gulp to keep my static assets up to date while running a self-hosted website: [Self-Hosted Web – Updating assets without restarting the debugger][1]

This post will use a similar approach, using gulp “watch” to watch for changes to *.spec.js files in the filesystem and rebuilding a list of specs in a RequireJS define statement, so we automatically will have an up-to-date spec list every time we add or remove a spec file, with no manual editing required.

_Note: Examples all use Jasmine and RequireJS throughout the post_

## Using Gulp to Create the Spec List

Here's a sample SpecRunner file that's relying on RequireJS to define the specs:

**SpecRunner.html**

```javascript






```
This SpecRunner directly includes only Jasmine, RequireJS, and a set of Require configs in main.js, everything else is loaded from the “allSpecs.js” file. The custom bootloader and window.executeTests() method replace the vanilla bootloader so we can make sure we load our spec files and their dependencies before running the tests (see [Unit Testing with Jasmine 2.0 and Require.JS][2] for more info).

The allSpecs file is simply a list of spec files in a RequireJS define() statement (currently only the first spec file for this tiny sample project)

**allSpecs.js**

```javascript
define(['spec/siteWideViewModel.spec.js',
], function(){ });
```
The advantage of doing this as a separate file is that we keep the change history for the mechanics of how we run the tests (SpecRunner) and the actual list of spec files (allSpecs) from crossing and greatly simplify future updates to the SpecRunner as newer versions of Jasmine come out as well as keep the text content we have to manage in our gulpfile to a minimum. Additionally, we can now use this “allSpecs” file in other test runners, like Karma or via a PhantomJS script, to ensure we're running exactly the same set of tests locally and in CI.

All we need now is to be able to build and maintain that allSpecs file. Using [Gulp][3], we can setup a task to watch the file system for any changes to files that match a pattern of *.spec.js. When we see a change, we can grab a full list of the spec files and concatenate that into a new define statement, overwriting the allSpecs file with an updated list.

**gulpfile.js**

```javascript
var gulp = require('gulp'),
    less = require('gulp-less'),
    watch = require('gulp-watch'),
    insert = require('gulp-insert'),
    concat = require('gulp-concat'),
    concatFilenames = require('gulp-concat-filenames'),
    argv = require('yargs').argv;;

var config = { };
config.assetsPath = "Assets";
config.assetsOutputPath = (argv.output || "bin/Debug/") + config.assetsPath;

gulp.task('watch', function () {
    // pick up new spec files and include them in the "allSpec" list
    // update one time when we start then watch for later updates
    regenerateAllSpecsFile();
    gulp.watch([ config.assetsPath + '/tests/spec/**/*.spec.js'], handleSpecChanges);
});

function handleSpecChanges(obj) {
    if (obj.type === 'added' || obj.type === 'deleted' || obj.type === 'renamed') {
        console.log('Spec ' + obj.type + ': ' + obj.path);
        regenerateAllSpecsFile();
    }
}

function regenerateAllSpecsFile() {
    gulp.src(config.assetsPath + '/tests/spec/**/*.spec.js')
        .pipe(concatFilenames('allSpecs.js', {
            root: config.assetsPath + "/tests",
            prepend: "'",
            append: "',"
        }))
        .pipe(insert.transform(function (contents, file) {
            return 'define([' + contents + '], function(){ });';
        }))
        .pipe(gulp.dest(config.assetsPath + "/tests"));
}
```
And there we go. Now as we add a new spec file or remove one, we don't have to also of update our SpecRunner file, just refresh the browser, keep working, and make sure the automatically updated file is in our source control push later.

 [1]: /index.php/webdev/self-hosted-web-updating-assets-without-restarting-the-debugger/
 [2]: /index.php/webdev/uidevelopment/javascript/unit-testing-with-jasmine-2-0-and-require-js/
 [3]: http://gulpjs.com/