---
layout: post
title:  "How the Web Works"
date:   2014-07-29 08:47:30
categories: Website
---

###How the web works.  At the Oregon Shakespeare Festival at least...
==================================================================

Perhaps you've never asked yourself how organizations like the Oregon Shakespeare Festival scale their web infrastructure to accomodate heavy customer load while still providing a rich user experience and speedy check out.  Maybe you should!  A number of incredibly interesting web technologies are out there every day helping you make the purchases you need during member pre-sale events, big thanksgiving day sales, and online events.

This is the story of the Oregon Shakespeare Festival's website www.osfashland.org and the improvements made to it's design to help handle lots of customers online.


###The Tech
--------------------------

Tons of cool technology is involved in making osfashland.org ticket sales a reality.

Amongst the tech is:

*  Gatling Stress Tests
*  CloudFlare
*  NGINX+
*  Dell VRTX Converged Infrastructure
*  vmWare vSphere 5
*  CentOS
*  Puppet
*  Chocolatey
*  PFSense
*  ASP.NET
*  SiteCore
*  Closure Compile


###How web sales go
--------------------------

In the figure below all of the components involved in selling a ticket on the web are outlined at a high level. Each component plays an important part in the scalability and redundancy that protects our user experience.

![WebArchitecture](/static/webstack-architechture.png)

###Our patronage
--------------------------

We know our patronage to be a diverse group of people using many different browsers out on the web.  At writing this article a 6 month sample of our users shows Safari on OS X to be the most popular browser at 39 % with Chrome and Internet Explorer following close behind.

Operating System Demographics put Windows first at 38.67% of market with iOS trailing just behind at 27.43%.  Much to our chagrin the mobile experience while improved is still lacking and will be the focus of next years preparations for the 2016 season pre-sale.


###CloudFlare
--------------------------



