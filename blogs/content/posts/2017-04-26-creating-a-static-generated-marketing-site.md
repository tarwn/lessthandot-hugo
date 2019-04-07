---
title: Creating a Static-Generated Marketing Site
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-04-26T21:37:41+00:00
ID: 8637
url: /index.php/webdev/creating-a-static-generated-marketing-site/
views:
  - 2338
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - metalsmith
  - static site generator

---
If you&#8217;re working on a free product or startup, you&#8217;re probably thinking frequently about how to balance your time across all the things that need doing. One of those millions of things is getting a marketing site up to tell people about your amazing thing, and do so in a way that looks professional, is easy to update and redeploy, doesn&#8217;t turn into a whole separate coding project, and so on.

Because I spend cycles thinking about things like this in my free time, I started looking into static site generators as a potential way to balance dynamic-enough with not-building-a-thing and low operational costs. You could have the freedom of hand-editable pages for things like your landing page, something minimal like markdown for blog posts, just enough markup to inject content from those blog posts into your landing page, but not have to build a whole back-end (and pay for extra back-end resources) to run it. 

So I decided to build a marketing site structure. It didn&#8217;t take long and you can feel free to grab a copy if you want to shave some time off and start where I left off: [tarwn/StaticMarketingSite on github][1]

## Anatomy of a Marketing Site

So my goal was to generate a marketing site that looked somewhat like this:

<div id="attachment_8643" style="width: 643px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/04/StaticSiteOutline.png" alt="Marketing Site Outline" width="633" height="347" class="size-full wp-image-8643" srcset="/wp-content/uploads/2017/04/StaticSiteOutline.png 633w, /wp-content/uploads/2017/04/StaticSiteOutline-300x164.png 300w" sizes="(max-width: 633px) 100vw, 633px" />
  
  <p class="wp-caption-text">
    Marketing Site Outline
  </p>
</div>

Using a static site generator, I could define a common layout to be used on every page, write the guts of the Features, Pricing, About, and Contact pages in static HTML, write HTML with some handlebars logic for the Landing page, and write markdown for the blog posts so I could focus strictly on content.

Lastly, I wanted a simple test, update, and deployment story. 

## Current Status

This is not a final state, but I&#8217;ll outline where I am now and what I&#8217;m considering changing. I&#8217;m using [Metalsmith][2] as the static site generator, with a mixture of HTML, Markdown, and [Handlebars][3] for content and the only local dependencies are [node.js][4] and [git][5].

### Build and Deployment

This is the place I spend the least amount of time now. 

After pulling the git repo down to a machine, I run `npm install` to install metalsmith and it&#8217;s plugins.

To build a fresh version of the site, I run `node install.js`

I also included a basic static webserver so I could test easily: `node static_server.js` and then point my browser at http://localhost:8888

Azure Websites support automatic deployment from a git repository, so I added a `.deployment` file to tell Azure only to serve up the contents of the /build folder. 

<div id="attachment_8647" style="width: 628px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/04/StatisSiteAzure.png" alt="Azure Deployment Log" width="618" height="298" class="size-full wp-image-8647" srcset="/wp-content/uploads/2017/04/StatisSiteAzure.png 618w, /wp-content/uploads/2017/04/StatisSiteAzure-300x145.png 300w" sizes="(max-width: 618px) 100vw, 618px" />
  
  <p class="wp-caption-text">
    Azure Deployment Log
  </p>
</div>

So my workflow is:

  * Start the static server in a console
  * Make some changes
  * Re-run `node install.js`
  * Refresh my browser
  * When happy: git commit, git push, wait a few seconds for it to go live

I don&#8217;t like the delay of step 3, so I may tighten this up further with a watch task so I can leave the index re-processing automatically in the background every time I save a file and cut down on the delay to seeing it in the browser. Next up would be cooking in a live refresh so I wouldn&#8217;t even need to refresh the browser.

### Adding a blog post

All it takes to add a new blog post is adding a markdown file to the blogs folder. The file has this format:

```markdown
---
title: My Awesome Post Title
date: 2012-08-20
layout: post.html
excerpt: summary of my post
---
Content of my post
```
The top section, or &#8220;front matter&#8221;, defines metadata used elsewhere in the build process. A &#8220;draft: true&#8221; value tells metalsmith to skip publishing the entry. The date is used when ordering the posts to decide what should show on the landing page and in the RSS feed. The &#8220;layout&#8221; property tells metalsmith to wrap this in the layouts/post.html content. The &#8220;excerpt&#8221; property is displayed on the landing page and in the RSS feed.

### Additional details

I included additional details in the README file with the git repo here: [README.md on tarwn/StaticMarketingSite][6]

## Building this structure

I built this site structure using [Metalsmith][2].

1. First I created a package.json file to make it easy to install the same packages on any machine I downloaded this on. Here is the [sample package file][7] I started learning from.

2. Next I decided on a site structure I wanted to target:

/ &#8211; landing page
  
/about
  
/contact
  
/docs
  
/docs/[doc-name]
  
/features
  
/posts
  
/posts/[post-name]
  
/pricing
  
plus a sitemap and a RSS feed

To build this, I used a number of [Metalsmith plugins][8].

Here is an annotated version of my index.js file:

```javascript
Metalsmith(__dirname)
  
  // key/value metadata available for all files in my site
  .metadata({
    title: "My Static Site & Blog",
    description: "It's about saying »Hello« to the World.",
    generator: "Metalsmith",
    url: "http://www.metalsmith.io/",

    // make rss feed happy
    site: {
      url: baseUrl
    }
  })

  // process files from /src and output them to /build
  .source('./src')
  .destination('./build')

  // clean the /build folder before rebuilding
  .clean(true)

  // ignore blog posts with "draft: true"
  .use(drafts())

  // process markdown files into HTML
  .use(markdown())

  // define set of all posts and posts for landing page
  .use(collections({
    latestPosts: {
      pattern: 'posts/**/*.html',
      sortBy: 'date',
      reverse: true,
      limit: 2
    },
    posts: {
      pattern: 'posts/**/*.html',
      sortBy: 'date',
      reverse: true
    }
  }))

  // convert filename.md to filename/index.html 
  .use(permalinks())

  // process handlebars in content files
  .use(hbtmd(handlebars, {
      pattern: '**/*.html'
  }))

  // process handlebars in layout files
  .use(layouts({
    engine: 'handlebars'
  }))

  // generate a sitemap file
  .use(sitemap({
    hostname: baseUrl,
    // defaults that can be overridden in individual files
    changefreq: "monthly",
    lastmod: new Date(),
    omitIndex: true
  }))

  // generate an RSS feed from the posts collection
  .use(feed({
    collection: "posts",
    limit: 10,
    destination: "rss.xml"
  }))

  // switch to "true" for debug output
  .use(debug(false))

  // and Go!
  .build(function(err, files) {
    if (err) { throw err; }
});
```
You can see the full file here: [index.js on tarwn/StaticMarketingSite][9].

## Things to Improve

LESS/SASS/CSS- I left these out on purpose because I think this should reflect the fastest/least friction option for the person building the site

Docs Layouts &#8211; I didn&#8217;t finish docs at all. I feel like this is going to be pretty custom to how someone would want to share information about their product (and what that product is), so it&#8217;s one of the bigger gaps

Image processing, Responsive frameworks, etc &#8211; These are choices that rely heavily on the design of the page, so I didn&#8217;t play with it at all

 [1]: https://github.com/tarwn/StaticMarketingSite "tarwn/StaticMarketingSite on github"
 [2]: http://metalsmith.io
 [3]: http://handlebarsjs.com/
 [4]: https://nodejs.org/en/
 [5]: https://git-scm.com/
 [6]: https://github.com/tarwn/StaticMarketingSite/blob/master/README.md
 [7]: https://github.com/segmentio/metalsmith/blob/master/examples/static-site/package.json
 [8]: http://www.metalsmith.io/#the-plugins
 [9]: https://github.com/tarwn/StaticMarketingSite/blob/master/index.js