---
layout: post
title:  "How the Web Works"
date:   2014-07-29 08:47:30
categories: Website
---

###How the web works.  At the Oregon Shakespeare Festival at least...
=======================================================================

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
*  NewRelic
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
CloudFlare is a "Cloud Based" web optimization platform.  It not only acts as reverse proxy but also does inline content acceleration.

*   Cache
*   Auto-minification
*   Image scaling per-device
*   DDOS-Protection
*   Site Offline Message
*   Fail Whale Pages ( Still needs chosen and done )

Are just a few of the things that we benefit from by virtue of the fact that we have cloudflare.

#### Recent Testing
Recent internal testing has revealed the CloudFlare reduces load on our web servers by **15-20%** during stress tests with Gatling tool.  This was tested loading 250 users across the farm at 1 user every two seconds.  Page load times were the most common metric used in measuring performance improvement.

#### Questions about CloudFlare
Due to the nature of the way CloudFlare intercepts and modifys traffic we have wondered if we should leverage CloudFlare's ability to pass extra information about the patron to NGINX.  NGINX may or may not be able to use this information to better maintain client state.

This is well documented here.

https://support.cloudflare.com/hc/en-us/articles/200504045-Using-CloudFlare-with-your-API

### DataCenter West Colocation
--------------------------------
DataCenter West as a facility was chosen largely because of it's peer exhchange.  The peer exchange is the direct connection of all the major bandwidth carriers in Southern Oregon.  This allows the best opportunity to stay online selling tickets in our region.

#### Issues with DataCenter West
On July 28, 2014 at approximately 4:18 Data Center West experienced a single phase power loss.  This creates an unbalanced load condition when the currents on the three live wires of a three-phase system are not equal or are not at an exact 120° phase angle, the power-loss is greater than for a perfectly balanced system.

The Dell VRTX chassis at the site experienced partial power loss.  This resulted in the network management controller losing part of it's configuration.  This was either due to the configuration not being saved to nvram or a problem with the management controller firmware.


### PFSense Firewall Service
--------------------------------

PFSense -- a firewall distribution based in FreeBSD with a great web interface was chosen to perform firewall services for the e-commerce system largely due to it's low cost, small attack surface, and agility.  PFSense is well support on the vSphere platform.

#### The Next Design Starship

The next design of the DataCenter west rack will include another out of band firewall to hold up SSL VPN between sites.  This will be advantageous in the event that Hunter Fails or Human Intervention is required.

The first candidate is:

http://store.netgate.com/Netgate-FW-7541-1U-Rack-Mount-System-BTO-P1903.aspx

![starship](/static/starship.jpg)

### NGINX
---------------------------------

NGINX is a reverse proxy first, load balancer second, and a web server third.  In the OSF implementation NGINX was selected due to it's ability to terminate SSL and act as a load balancer.

NGINX provides load balancing for both the SiteCore delivery server and Jacobson Consulting Associates select your own seat.

NGINX was originally slated for load balancing of the CRM/ERP service layer ( in this case Tessitura SOAP ) but was scrapped due to the integration of the SOAP services on the web server using a binding to localhost.

SSL is also terminated at the reverse proxy.

>Any request arriving in the clear is rewritten to SSL and the backend remains http.

    rewrite        ^ https://$server_name$request_uri? permanent;


Another article has a detailed brief on the NGINX configuration available from the parent site.

### SiteCore Delivery servers
----------------------------------

OSF's web server configuration is quite unique in that three distinctly different applications are required to run to make the site go.

*     OSFAshland SiteCore Application
*     Select your own seat service
*     Tessitura Service Layer


#### Tessitura Service Layer
The tessitura service layer is unique to OSF's sitecore deployment.  This is representative of the soap services needed to integrate with the ERP/CRM Tessitura.  Rather than go parallel with the Tessitura service layer we elected to use web gardens on the web server, employ the ASP.NET state service, and parallelize threads on each web server.

Additionally the queue lengths on the application workers were also increased from their default setting of 1000 to 5000.

#### Select your own seat service
The select your own seat service by Jacobson Consulting and Associates uses the Tessitura service layer, javascript, and SVG objects to facilitate the generation of a seat map.  The service also has logic in it to evaluate membership levels.

>Completely anecdoteally we have never been able to load test this service since it is so javascript heavy.  We can do functional tests in Selenium.  We also have not validated that this is 'A' OKAY with web gardens.  Several calls have pointed to angular JS and an excellent candidate to rewrite this if we ever want to completely re-write the UI.

#### SiteCore Application
The SiteCore application configuration is largely in the web.config files.  SiteCore itself does not support web gardens.


SiteCore's caches per delivery server have been optimized according to the following guide:

>http://sdn.sitecore.net/Articles/Administration/Sitecore%20Performance/Optimizing%20Sitecore%206%20and%20later/Optimizing%20Performance%20in%20Sitecore.aspx

The sitecore instance on each web server is connected to the set of tessitura services on it's own web node.

#### Tessitura Database Server





