---
title: 'Elasticsearch and my setup. Part 1: the why and the how.'
author: Christiaan Baes (chrissie1)
type: post
date: 2017-06-20T17:53:24+00:00
ID: 8675
url: /index.php/uncategorized/elasticsearch-and-my-setup-part-1-the-why-and-the-how/
rp4wp_auto_linked:
  - 1
views:
  - 1919
categories:
  - Uncategorized

---
# Introduction

Over the past few years I used elasticsearch, one bit for using as a fulltextsearch engine and one bit as using to store logdata.
  
Somehow I ended up with 2 seperate elasticsearch server, each forming their own cluster. The cluster was in a yellow state most of the time if not red the ther time. Mostly caused by unassigned clusters. By default elastic tries to replicate every shard at least once. When you have only one node in your cluster it can't get rid of the shard. So you end up with unassigned shards and thus a cluster in a yellow state. Which is annoying to say the least. You want things to be nice and green. So I needed to setup a new cluster and copy some data over from the old clusters. Starting fresh is always a good idea.
  
My previous elastic machines were windows machines. But let's be honest linux is the way forward here. 

# The stack

Of course I want the whole ELK (Elastic, logstash and kibana). So that means setting up 5 servers from scratch.
  
What better way to make a machine than to use [packer][1]? Since you can make VM's from the ISO and build from there. 

So I used 

  * ElasticSearch 5.4.0
  * Kibana 5.4.0
  * Logstash 5.4.0
  * Ubuntu server edition 17.04
  * X-pack 5.4.0
  * Packer (latest version)
  * Virtualbox 5.something

For ease of use I downloaded the iso file for ubuntu the deb files for Elastic, Kibana and logstash and the zip file ofr X-pack. These files can be found by googling for them. 

# The setup

So you create a folder somewhere and then you [git clone][2] the repo. Which should probably be online sometime later tonight.
  
The repo does not contain the zip,deb or iso-files. 

# Why?

This will create 5 servers for which you will need a lot of memory to run them 3\*24GB and 2\*8GB that's... a lot of memory. So why do the elasticsearch servers have so much RAM. Becuase that's what I found on the internet to be the best. 12GB for elastic and 12GB for the full text index engine. I only have a few million documents a day and have yet to see it use all the memory.
  
The disks on the servers are 200GB. And they use 4vCPUs. But since this is a script you can change all this.
  
The kibana and logstash server each have their own elastic node that doesn't have data or isn't a master.
  
3 elastic masters is great since this way they work as a RAID5 setup and you can reboot one while the cluster keeps going. 

this does not yet configure the NIC but the ip-adresses should be.

  * Elasticsearch01: 192.168.0.10
  * Elasticsearch02: 192.168.0.11
  * Elasticsearch03: 192.168.0.12
  * Kibana01: 192.168.0.13
  * Logstash01: 192.168.0.14

If you want to change the ip-adresses than you also have to change the config files in the config folders.

In some later blogposts I will go into the config a bit further, and try to explain my deciscions.

 [1]: https://www.packer.io/
 [2]: https://github.com/chrissie1/packerelastic