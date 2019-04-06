---
title: Bundling with the RequireJS Optimizer
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2015-02-19T11:50:58+00:00
url: /index.php/webdev/bundling-with-the-requirejs-optimizer/
views:
  - 2747
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - Web Developer
tags:
  - javascript
  - requirejs

---
When we build sites using an AMD library like [RequireJS][1], we will have a long list of files that need to be downloaded when someone uses the site. More files means more trips to the server and more download time. Minifying files and using gzip can speed up the download times, but neither affects the Round Trip Time (RTT) that even a cache validation incurs, clogging up a request pipeline just to ask the server if an ETag or last modified date is still valid.

So let&#8217;s see one way we can improve things, with small (14 requests) and larger (194 requests) page loads as an example.

## RequireJS Bundling

There are several different ways you can bundle scripts with RequireJS, but I wanted to start out exploring their [optimizer][2]. The advantage of using the optimizer is that it can intelligently trace the module dependencies and include them for me rather than requiring me to figure out all the dependencies and ensure I bundle them up in the right grouping and order.

I started with a very small application just to play around and see what the impact would be. It consists of an HTML page, a couple 3rd party libraries, and 10 JS files I wrote. On loading the page, it uses a require statement to load two of the files (and thus all of their dependencies). My plan is to build a replacement set so I can load a single file and have it also load in all of the dependencies, in the right order to prevent independent network requests for other dependencies.

Sample Site Structure:

<pre>town/
   index.html
   js/
      lib/
         jquery.js
         knockout.js
         require.js
         Squire.js
         jasmine/
            ... jasmine files ...
      src/
         app.js
         ... 9 more hand-written files ...
      test/
         ... several JS spec files ...
   styles/
         ... css file ...
   images/
         ... image files ...
tools/
   r.js
   ... my config will go here ...
js-built/ 
   ... bundle + minified files will be created here ...</pre>

This project also has some test files mixed in both the lib folder and a parallel test folder, which we want to exclude from processing at all (on a larger project this would be going through a build process, no point eating up CPU time minifying files that will never go to production).

You can feed the optimizer either command-line options or an options file, I suggest putting everything in a configuration file for repeatability (and readability).

<pre>{
    appDir: '../town/js',
    baseUrl: 'src',
    paths: {
        knockout: '../lib/knockout-3.0.0'
    },
    dir: '../js-built',
    fileExclusionRegExp: /(^test|Squire|jasmine|require)/,
    modules: [
        {
            name: 'app',
            include: ['app', 'townViewModel']
        }
    ]
}</pre>

The optimizer produces a new &#8220;app.js&#8221; file for me in the js-built folder and I can copy that over my existing source file. Notice how I did not have to define every single file, the optimizer will take the two modules in the &#8220;include&#8221; and trace all of their dependencies for me. There is also an option to exclude individual files or other defined bundles.

Config Translation:

  * appDir: The path to the js file, relative to the tools directory where r.js lives (not relative to where we execute node from)
  * baseUrl: Base URL used for RequireJs modules, relative to that appdir (Further note below)
  * paths: RequireJS paths (had I been using a config file w/ RequireJS, I could have supplied that instead of redefining paths here)
  * dir: Output directory (also the working directory for the optimizer), relative to the r.js file again
  * fileExclusionRegExp: the optimizer ignores any file or directory that matches this regular expression (Further note below)
  * modules: an array of modules to build, which can depend on earlier modules (this is a small app so I put everything in a single module)

As I worked with this smaller example and a much larger one, here are some issues I ran into:

  * appDir: I ran into problems defining appDir too deeply and had to define it at the shared higher level (but only on the larger project, so this may be a side effect of the next item)
  * paths: On the larger project I had a number of paths defined with a starting slash, which works fine for a site but the optimizer translates as &#8220;look on the root of the drive&#8221;, not seeing any reason for those to be root paths, I fixed them in my main RequireJS config to be relative
  * fileExclusionRegExp: I attempted to invert this into an opt-in list using negative lookaheads, but was unable to get it to match more than one value for lookaheads, despite testing the expression elsewhere
  * optimize: can be used to turn off minification, which was necessary before I figured out how to filter out some 3rd party files that the optimizer would exit with an error over

I did run into some other issues, here and there, but unfortunately was not keeping track of them at the time.

## Results

To work around the &#8220;localhost is crazy fast&#8221; issue, we can use Chrome to load sites with throttled connections (Dev Tools, Toggle Device mode with the phone icon, change the Network dropdown). For these results I used the 3G option (100 RTT), which is only about 10% slower than the ping from my house to my personal website and at 750kbps, matches the type of shared bandwidth people might see if their company is keeping costs low and over-utilizing a cheaper internet connection. Improvements we make for our slower visitors only makes the experience that much better for our faster ones.

### Sample Site Results

I ran the site with and without caching enabled, refreshing and capturing only the best possible result I saw. Here&#8217;s what I saw for the Vanilla site, with and without caching:

<div id="attachment_3172" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/02/100RTT_Vanilla.png"><img src="/wp-content/uploads/2015/02/100RTT_Vanilla.png" alt="Small Sample Load - 100RTT/750kbps - Vanilla" width="451" height="30" class="size-full wp-image-3172" srcset="/wp-content/uploads/2015/02/100RTT_Vanilla.png 451w, /wp-content/uploads/2015/02/100RTT_Vanilla-300x19.png 300w" sizes="(max-width: 451px) 100vw, 451px" /></a>
  
  <p class="wp-caption-text">
    Small Sample Load &#8211; 100RTT/750kbps &#8211; Vanilla
  </p>
</div>

<div id="attachment_3173" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/02/100RTT_Vanilla_Cache.png"><img src="/wp-content/uploads/2015/02/100RTT_Vanilla_Cache.png" alt="Small Sample Load - 100RTT/750kbps - Vanilla, Cached" width="447" height="23" class="size-full wp-image-3173" srcset="/wp-content/uploads/2015/02/100RTT_Vanilla_Cache.png 447w, /wp-content/uploads/2015/02/100RTT_Vanilla_Cache-300x15.png 300w" sizes="(max-width: 447px) 100vw, 447px" /></a>
  
  <p class="wp-caption-text">
    Small Sample Site &#8211; 100RTT/750kbps &#8211; Vanilla Cached
  </p>
</div>

After using the optimizer (bundled and minified), the best results I received were:

<div id="attachment_3174" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/02/100RTT_BundledMinified.png"><img src="/wp-content/uploads/2015/02/100RTT_BundledMinified.png" alt="Small Sample Load - 100RTT/750kbps - Bundled, Minified" width="446" height="26" class="size-full wp-image-3174" srcset="/wp-content/uploads/2015/02/100RTT_BundledMinified.png 446w, /wp-content/uploads/2015/02/100RTT_BundledMinified-300x17.png 300w" sizes="(max-width: 446px) 100vw, 446px" /></a>
  
  <p class="wp-caption-text">
    Small Sample Load &#8211; 100RTT/750kbps &#8211; Bundled, Minified
  </p>
</div>

<div id="attachment_3175" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/02/100RTT_BundledMinified_Cache.png"><img src="/wp-content/uploads/2015/02/100RTT_BundledMinified_Cache.png" alt="Small Sample Load - 100RTT/750kbps - Bundled, Minified, Cached" width="436" height="23" class="size-full wp-image-3175" srcset="/wp-content/uploads/2015/02/100RTT_BundledMinified_Cache.png 436w, /wp-content/uploads/2015/02/100RTT_BundledMinified_Cache-300x15.png 300w" sizes="(max-width: 436px) 100vw, 436px" /></a>
  
  <p class="wp-caption-text">
    Small Sample Load &#8211; 100RTT/750kbps &#8211; Bundled, Minified, Cached
  </p>
</div>

The best vanilla load was 14 requests at 1.88 seconds, with a best cache time of 456ms. The optimized version reduced this to 4 requests at 1.44 seconds, with a best cache of 311ms.

### Larger Site Results

While there was a visible difference in the small site, I also wanted to see what would happen in a larger example. The larger site has almost 200 requests, including AJAX calls to an external API and numerous image and CSS resources that have not been optimized yet. Like the small example above, we are not using gzip in this example. Using the same 100RTT setting in chrome (which also impacts us more in this case, due to the 750kbps speed), here are before and after timings:

<div id="attachment_3180" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/02/Large_100RTT_Vanilla.png"><img src="/wp-content/uploads/2015/02/Large_100RTT_Vanilla.png" alt="Large Sample Load - 100RTT/750kbps - Vanilla" width="447" class="size-full wp-image-3180" srcset="/wp-content/uploads/2015/02/Large_100RTT_Vanilla.png 894w, /wp-content/uploads/2015/02/Large_100RTT_Vanilla-300x14.png 300w" sizes="(max-width: 894px) 100vw, 894px" /></a>
  
  <p class="wp-caption-text">
    Large Sample Load &#8211; 100RTT/750kbps &#8211; Vanilla
  </p>
</div>

<div id="attachment_3181" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/02/Large_100RTT_Vanilla_Cached.png"><img src="/wp-content/uploads/2015/02/Large_100RTT_Vanilla_Cached.png" alt="Large Sample Load - 100RTT/750kbps - Vanilla, Cached" width="438" class="size-full wp-image-3181" srcset="/wp-content/uploads/2015/02/Large_100RTT_Vanilla_Cached.png 877w, /wp-content/uploads/2015/02/Large_100RTT_Vanilla_Cached-300x18.png 300w" sizes="(max-width: 877px) 100vw, 877px" /></a>
  
  <p class="wp-caption-text">
    Large Sample Load &#8211; 100RTT/750kbps &#8211; Vanilla, Cached
  </p>
</div>

After using the optimizer to create 2 minified bundles:

<div id="attachment_3178" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/02/Large_100RTT_BundledMinified.png"><img src="/wp-content/uploads/2015/02/Large_100RTT_BundledMinified.png" alt="Large Sample Load - 100RTT/750kbps - Bundled, Minified" width="438" class="size-full wp-image-3178" srcset="/wp-content/uploads/2015/02/Large_100RTT_BundledMinified.png 877w, /wp-content/uploads/2015/02/Large_100RTT_BundledMinified-300x16.png 300w" sizes="(max-width: 877px) 100vw, 877px" /></a>
  
  <p class="wp-caption-text">
    Large Sample Load &#8211; 100RTT/750kbps &#8211; Bundled, Minified
  </p>
</div>

<div id="attachment_3179" style="width: 510px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2015/02/Large_100RTT_BundledMinified_Cached.png"><img src="/wp-content/uploads/2015/02/Large_100RTT_BundledMinified_Cached.png" alt="Large Sample Load - 100RTT/750kbps - Bundled, Minified, Cached" width="429" class="size-full wp-image-3179" srcset="/wp-content/uploads/2015/02/Large_100RTT_BundledMinified_Cached.png 858w, /wp-content/uploads/2015/02/Large_100RTT_BundledMinified_Cached-300x14.png 300w" sizes="(max-width: 858px) 100vw, 858px" /></a>
  
  <p class="wp-caption-text">
    Large Sample Load &#8211; 100RTT/750kbps &#8211; Bundled, Minified, Cached
  </p>
</div>

The best vanilla load is 194 requests at 16.18s, which drops to 2.91 seconds with cache. With bundling and minification, that drops to 31 requests at 10.3 seconds, which drops to 27 requests and 2.89 seconds with cache.

The configuration for this site continued to be almost as light-weight as the small sample site above, so despite the number of files increasing by greater than an order of magnitude, the ability for the optimizer to trace those dependencies for me meant that I was able to bundle all of these files with a configuration that was only about twice as long as the small sample site above.

 [1]: http://requirejs.org/
 [2]: http://requirejs.org/docs/optimization.html