---
title: Elasticsearch
author: Christiaan Baes (chrissie1)
type: post
date: 2013-12-14T09:55:00+00:00
ID: 2208
excerpt: Getting up and going with ElasticSearch and Mint.
url: /index.php/datamgmt/datadesign/elasticsearch/
views:
  - 12956
categories:
  - Data Modelling and Design
tags:
  - elasticsearch

---
## Introduction

I heard of [Elasticsearch][1] before but while at NDC-London I saw it put to work and I immediately fell i love with it.

This is how software is supposed to work. Install and let it figure out itself. 

Elasticsearch is a distributed restful search and analytics server based on Lucene. So if you feel the need to add fulltext index searching to your application this is the software to go for. 

## Installation

The one thing Elasticsearch claims is that it is easy to install and get setup in a cluster. Since Linux is cheap and cheerful I decided to download and install [Mint][2] 16 as a VM and install ElasticSearch on that. I <3 mint BTW (and now I&#8217;m a hipster, right?)

Once you have Mint installed go to the [Elasticsearch download page][3] and select the DEB package that will install just fine on Mint.

Once you have done that goto your favorite browser and check if it installed ok by going to http://localhost:9200 which should give you this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ElasticSearch/ElasticSearch1.png?mtime=1387013631"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ElasticSearch/ElasticSearch1.png?mtime=1387013631" width="1163" height="723" /></a>
</div>

The install also set up the firewall so that port is now also available from your host machine.

## Playing

Now that we have set it up and also tried it from our host machine we can start playing around with it. I would recommend reading the [blogarticle][4] by Joel Abrahamsson. 

I would also recommend using chrome and install [the sense extension][5] to get started.

And here are the results of the Belgian jury.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ElasticSearch/ElasticSearch2.png?mtime=1387014155"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ElasticSearch/ElasticSearch2.png?mtime=1387014155" width="773" height="719" /></a>
</div>

Go read the blog by Joel to get you going.

Here a few of the commands I used.

```
PUT /plants/plant/1
{
    "name":"Quercus"
}
```
The above command will create an index if it not yet exists, create the type if it not yet exists and create the entry with an id of 1 and put that object with a name property and a value of Quercus in it.
  
If you run that command again it will not overwrite the object but create a new version instead.

This being the result.

> {
     
> &#8220;ok&#8221;: true,
     
> &#8220;_index&#8221;: &#8220;plants&#8221;,
     
> &#8220;_type&#8221;: &#8220;plant&#8221;,
     
> &#8220;_id&#8221;: &#8220;1&#8221;,
     
> &#8220;_version&#8221;: 1
  
> } 

```
PUT /plants/plant/2
{
    "name":"Fagus"
}
```
The above will just create a second entry with an id of 2 in the same index.
  
With this as the result.

> {
     
> &#8220;ok&#8221;: true,
     
> &#8220;_index&#8221;: &#8220;plants&#8221;,
     
> &#8220;_type&#8221;: &#8220;plant&#8221;,
     
> &#8220;_id&#8221;: &#8220;2&#8221;,
     
> &#8220;_version&#8221;: 1
  
> } 

```
GET /plants/plant/1
```
The above will get you the entry with id 1 from the index plants with a type plant.
  
With this as the result.

> {
     
> &#8220;_index&#8221;: &#8220;plants&#8221;,
     
> &#8220;_type&#8221;: &#8220;plant&#8221;,
     
> &#8220;_id&#8221;: &#8220;1&#8221;,
     
> &#8220;_version&#8221;: 1,
     
> &#8220;exists&#8221;: true,
     
> &#8220;_source&#8221;: {
        
> &#8220;name&#8221;: &#8220;Quercus&#8221;
     
> }
  
> }

```
GET /plants/plant/2
```
The above will get you the entry with id 2 from the index plants with a type plant.
  
With this as the result.

> {
     
> &#8220;_index&#8221;: &#8220;plants&#8221;,
     
> &#8220;_type&#8221;: &#8220;plant&#8221;,
     
> &#8220;_id&#8221;: &#8220;2&#8221;,
     
> &#8220;_version&#8221;: 1,
     
> &#8220;exists&#8221;: true,
     
> &#8220;_source&#8221;: {
        
> &#8220;name&#8221;: &#8220;Fagus&#8221;
     
> }
  
> }

```
POST /plants/plant/_search
{
    "query": {
        "query_string": {
            "query": "*u*"
        }
    }
}```
The above will search for any entry with a u in it.

With this as the result.

> {
     
> &#8220;took&#8221;: 9,
     
> &#8220;timed_out&#8221;: false,
     
> &#8220;_shards&#8221;: {
        
> &#8220;total&#8221;: 5,
        
> &#8220;successful&#8221;: 5,
        
> &#8220;failed&#8221;: 0
     
> },
     
> &#8220;hits&#8221;: {
        
> &#8220;total&#8221;: 2,
        
> &#8220;max_score&#8221;: 1,
        
> &#8220;hits&#8221;: [
           
> {
              
> &#8220;_index&#8221;: &#8220;plants&#8221;,
              
> &#8220;_type&#8221;: &#8220;plant&#8221;,
              
> &#8220;_id&#8221;: &#8220;2&#8221;,
              
> &#8220;_score&#8221;: 1,
              
> &#8220;_source&#8221;: {
                 
> &#8220;name&#8221;: &#8220;Fagus&#8221;
              
> }
           
> },
           
> {
              
> &#8220;_index&#8221;: &#8220;plants&#8221;,
              
> &#8220;_type&#8221;: &#8220;plant&#8221;,
              
> &#8220;_id&#8221;: &#8220;1&#8221;,
              
> &#8220;_score&#8221;: 1,
              
> &#8220;_source&#8221;: {
                 
> &#8220;name&#8221;: &#8220;Quercus&#8221;
              
> }
           
> }
        
> ]
     
> }
  
> }

Now go and play with it yourself. 

## Conclusion

ElasticSearch is very easy to set up and get going and very easy to use with it&#8217;s RESTFull interface (whatever REST may be). This was just a post to get you interested. But I&#8217;m sure I was the last person in this world to get in to this.

 [1]: http://www.elasticsearch.org/overview/
 [2]: http://www.linuxmint.com/
 [3]: http://www.elasticsearch.org/download/
 [4]: http://joelabrahamsson.com/elasticsearch-101/
 [5]: https://github.com/bleskes/sense/