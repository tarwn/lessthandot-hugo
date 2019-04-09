---
title: Exploring Reactive Extensions – Implications for Parallelism
author: Alex Ullrich
type: post
date: -001-11-30T00:00:00+00:00
ID: 963
excerpt: |
  I'm pretty sure this is going to be necessary post :)
  
  OS - level threads are good, but they will only take you so far (and kind of back you into a corner)
  
  Reactive programming allows you to send your data to other machines for processing.  Major s&hellip;
draft: true
url: /?p=963
categories:
  - Microsoft Technologies

---
I'm pretty sure this is going to be necessary post 🙂

OS &#8211; level threads are good, but they will only take you so far (and kind of back you into a corner)

Reactive programming allows you to send your data to other machines for processing. Major shift in system design, but could allow for much more throughput.