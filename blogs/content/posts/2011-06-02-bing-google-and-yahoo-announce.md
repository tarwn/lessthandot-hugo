---
title: Bing, Google and Yahoo! announce Schema.org
author: SQLDenis
type: post
date: 2011-06-02T15:32:00+00:00
ID: 1200
excerpt: |
  Bing, Google and Yahoo! have announced Schema.org
  
  Here is the info
  
  
  Today we’re announcing schema.org, a new initiative from Yahoo!, Bing, and Google, to create and support a common set of schemas for structured data markup on web pages. With sch&hellip;
url: /index.php/webdev/webdesigngraphicsstyling/bing-google-and-yahoo-announce/
views:
  - 9970
rp4wp_auto_linked:
  - 1
categories:
  - AJAX
  - Web Design, Graphics and Styling
  - XHTML and CSS
tags:
  - html
  - markup

---
Bing, Google and Yahoo! have announced [Schema.org][1]

Here is the info

Today we’re announcing schema.org, a new initiative from Yahoo!, Bing, and Google, to create and support a common set of schemas for structured data markup on web pages. With schema.org, webmasters and developers can learn about structured data and improve how their sites appear in search results on Bing, Google, and Yahoo!. Information and tips are available on schema.org, a one-stop resource for webmasters looking to add markup to make their pages better understood by search engines.

## What is Schema.org?

This site provides a collection of schemas, i.e., html tags, that webmasters can use to markup their pages in ways recognized by major search providers. Search engines including Bing, Google and Yahoo! rely on this markup to improve the display of search results, making it easier for people to find the right web pages.
  
Many sites are generated from structured data, which is often stored in databases. When this data is formatted into HTML, it becomes very difficult to recover the original structured data. Many applications, especially search engines, can benefit greatly from direct access to this structured data. On-page markup enables search engines to understand the information on web pages and provide richer search results in order to make it easier for users to find relevant information on the web. Markup can also enable new tools and applications that make use of the structure.
  
A shared markup vocabulary makes easier for webmasters to decide on a markup schema and get the maximum benefit for their efforts. So, in the spirit of sitemaps.org, Bing, Google and Yahoo! have come together to provide a shared collection of schemas that webmasters can use.

Main site here: http://schema.org/

getting started site is here: http://schema.org/docs/gs.html

An example

Orginal HTML

```html
Jane Doe
<img src="janedoe.jpg" />

Professor
20341 Whitworth Institute
405 Whitworth
Seattle WA 98052
(425) 123-4567
<a href="mailto:jane-doe@xyz.edu">jane-doe@illinois.edu</a>

Jane's home page:
<a href="www.janedoe.com">janedoe.com</a>

Graduate students:
<a href="www.xyz.edu/students/alicejones.html">Alice Jones</a>
<a href="www.xyz.edu/students/bobsmith.html">Bob Smith</a>
```
With MicroData

```html
<div itemscope itemtype="http://schema.org/Person">
  <span itemprop="name">Jane Doe</span>
  <img src="janedoe.jpg" itemprop="image" />

  <span itemprop="jobTitle">Professor</span>
  <div itemprop="address" itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">
      20341 Whitworth Institute
      405 N. Whitworth
    </span>
    <span itemprop="addressLocality">Seattle</span>,
    <span itemprop="addressRegion">WA</span>
    <span itemprop="postalCode">98052</span>
  </div>
  <span itemprop="telephone">(425) 123-4567</span>
  <a href="mailto:jane-doe@xyz.edu" itemprop="email">
    jane-doe@xyz.edu</a>

  Jane's home page:
  <a href="www.janedoe.com" itemprop="url">janedoe.com</a>

  Graduate students:
  <a href="www.xyz.edu/students/alicejones.html" itemprop="colleagues">
    Alice Jones</a>
  <a href="www.xyz.edu/students/bobsmith.html" itemprop="colleagues">
    Bob Smith</a>
</div>
```
What do you think, will you start implementing this?

 [1]: http://schema.org/