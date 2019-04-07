---
title: Self-Hosted Web – Updating assets without restarting the debugger
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-07-01T17:43:54+00:00
ID: 4600
url: /index.php/webdev/self-hosted-web-updating-assets-without-restarting-the-debugger/
views:
  - 4086
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - Gulp
  - less
  - WebAPI

---
When you work with an ASP.Net project through Visual Studio, you can edit static files like CSS and JS files and see them immediately in your browser. Switch to a console application and self-hosted option, such as a self-hosted WebAPI or NancyFX site, and you&#8217;ll find that editing Content files will require restarting the debugger to see the changes.

I&#8217;ve lived with this in the past, but the regular delay became annoying so I decided to find a solution.

## Why don&#8217;t the new files show up?

Web Projects run directly out of the project folder, so they are serving up the same version of the files we are editing, allowing real-time editing to be possible. With console applicaitons, we are editing the file in the project and relying on it being set to &#8220;Content&#8221; and &#8220;Copy Always&#8221; so the compiler will copy that content when we build. Since the application runs out of the bin folder, it&#8217;s limited to that out of date copy of the files.

<div id="attachment_4601" style="width: 624px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/06/AssetsFolder.png"><img src="/wp-content/uploads/2016/06/AssetsFolder.png" alt="Example Assets Folder" width="614" height="234" class="size-full wp-image-4601" srcset="/wp-content/uploads/2016/06/AssetsFolder.png 614w, /wp-content/uploads/2016/06/AssetsFolder-300x114.png 300w" sizes="(max-width: 614px) 100vw, 614px" /></a>
  
  <p class="wp-caption-text">
    Example Assets Folder
  </p>
</div>

So our edits are saved, but because we&#8217;re actually seeing a copy of the file in our browser, we don&#8217;t see the updates.

## Using Gulp to Push Updates to the bin folder

<a href="http://gulpjs.com/" target="_blank" title="gulp.js">Gulp</a> is a JavaScript task runner that uses file pipes to pick up files, run them through transformations, then output them. The project I was working on was already using a gulp task to transpile a LESS file into CSS, so it made sense to extend it to monitor all of my static assets and copy them to the bin folder when one of them changed.

If you haven&#8217;t used Gulp before, here is a good starter post: <a href="https://css-tricks.com/gulp-for-beginners/" title="CSS Tricks: Gulp for Beginners" target="_blank">CSS Tricks: Gulp for Beginners</a>

I only have two tasks, and both are related to building or debugging my project, so my project.json file is fairly short (and I used dependencies rather than devDependencies):

**package.json**

```javascript
{
  "name": "xyz-tooling",
  "version": "0.1.0",
  "description": "tooling for building/testing xyz local UI",
  "dependencies": {
    "gulp": "~3.9.1",
    "gulp-less": "~3.1.0",
    "gulp-watch": "~4.3.8",
    "gulp-concat": "~2.6.0",
    "gulp-insert": "~0.5.0",
    "yargs": "~4.7.1"
  }
}
```
The key ingredient is <a href="https://www.npmjs.com/package/gulp-watch" title="gulp-watch on npmjs.com" target="_blank">gulp-watch</a>. The &#8220;watch&#8221; task takes a wildcard path and watches the filesystem for changes to that path. When a file matching the path is changed, it calls the provided gulp tasks or function callback.

I have two tasks for gulp: 

  * &#8220;less&#8221; &#8211; transpiles all *.less files in my assets folder and concatenates them into a single stylesheet.css file
  * &#8220;watch&#8221; &#8211; watches the contents of my assets folder and either runs them through the &#8220;less&#8221; task or copies them to the bin/Debug/Assets folder

**gulpfile.json**: Configuration

```javascript
var gulp = require('gulp'),
    less = require('gulp-less'),
    watch = require('gulp-watch'),
    insert = require('gulp-insert'),
    concat = require('gulp-concat'),
    argv = require('yargs').argv;;

// Configure
var config = { };
config.assetsPath = "Assets";
config.assetsOutputPath = (argv.output || "bin/Debug/") + config.assetsPath;
```
I set a few configurations at the top of the file, including the name of the local Assets folder and an overridable path to the target Assets folder (which defaults to &#8220;bin/Debug/&#8221;).

**gulpfile.json**: &#8220;less&#8221; Task

```javascript
gulp.task('less', function() {
    gulp.src(config.assetsPath + '/*.less')
        .pipe(less())
        .pipe(insert.transform(function(contents, file) {
            console.log('LESS Compilation: ' + file.path)
            var comment = '/************ local file: ' + file.path + ' ************/\n';
            return comment + contents;
        }))
        .pipe(concat('stylesheet.css'))
        .pipe(gulp.dest(config.assetsPath))
});
```
The &#8220;less&#8221; task picks up all *.less files in the assets path, performs the LESS transpile on them, adds a prefix on the top of each file (this is a handy way to dynamically add copyright statements too), then concatenates to a single file.

In VisualStudio, I have a pre-build event that runs this command so I am executing my LESS transpile the same exact way if I do a Visual Studio build or the &#8220;watch&#8221; call later (the output stylesheet.css is ignored in my &#8220;.gitignore&#8221;, so my build process can use this same command as well).

**gulpfile.json**: &#8220;watch&#8221; Task

```javascript
// Task: watch
gulp.task('watch', function () {
    gulp.watch([ config.assetsPath + '/fonts/*.*',
                 config.assetsPath + '/images/*.*',
                 config.assetsPath + '/libs/*.*',
                 config.assetsPath + '/src/*.*',
                 config.assetsPath + '/*.js',
                 config.assetsPath + '/*.html',
                 config.assetsPath + '/*.css' ], handleChangedAssetFile);

    // pick up LESS files and compile them, 1st watch should pick up output and put it in correct spot
    gulp.watch(config.assetsPath + '/*.less', ['less']);
});

function handleChangedAssetFile(obj) {
    if (obj.type === 'changed') {
        console.log('Asset Change: ' + obj.path)
        gulp.src(obj.path, { "base": config.assetsPath })
            .pipe(gulp.dest(config.assetsOutputPath));
    }
}
```
I have two sets of files I want to handle, my LESS files and everything else. 

<u>Everything Else:</u> The first watch has everything except the LESS files and when a change occurs, it calls the handleChangedAssetFile method. This specifically targets only file changes, as I want to stop the debugger when I add a new file so I can remember to mark it as Content and Copy Always in the Properties pane. I also output the filename to the console as an easy way to confirm that a change has or has not been copied (it always is, but it&#8217;s easy to second guess when you make a change and the new file doesn&#8217;t do what you expect).

<u>LESS Files:</u> The LESS file watch simply triggers the previously defined &#8216;less&#8217; task, which will transpile the LESS files again and produce a new stylesheet.css. When the updated stylesheet.css file is saved, the first watch notices the change and copies it to the bin folder.

## Using It

I already keep a powershell console window open in the background in my project folder, so it&#8217;s no big deal to start the watch task and let it live back there in the background while I work (and it can continue running independent of starting and stopping the debugger, so I don&#8217;t have to ever think about it). The project I&#8217;m working on is still fairly small (frontend with ~ 10 screens/viewmodels, 5-6 services) and at this time it can recreate and copy the stylesheet faster than I can Alt+Tab to my browser and press F5.

<div id="attachment_4602" style="width: 541px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2016/06/CommandLine.png"><img src="/wp-content/uploads/2016/06/CommandLine.png" alt="Powershell Console" width="531" height="152" class="size-full wp-image-4602" srcset="/wp-content/uploads/2016/06/CommandLine.png 531w, /wp-content/uploads/2016/06/CommandLine-300x85.png 300w" sizes="(max-width: 531px) 100vw, 531px" /></a>
  
  <p class="wp-caption-text">
    Powershell Console
  </p>
</div>

My workflow now is to start the debugger that launches my console app, which launches a self-hosted WebAPI project, launch the watch task in my powershell window, then start working. The N-second delay of stopping and restarting the debugger no longer exists. As a secondary option, I can directly run the executable in my bin folder and work on the static assets without the debugger as well.