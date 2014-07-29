---
layout: post
title:  "Network Diagram"
date:   2014-07-29 08:47:30
categories: Network
---

Networking at DataCenter West
==============================

The network infrastructure connecting DataCenter West and the Oregon Shakespeare Festival is quite complex.  In order to fully undertand the reader should be familiar with the concept of
  *vlans
  *L2 lans
  *NAT
  *embedded virtual networks
  *vmWare vSphere networks
  *PFSense


Intersite Diagram
-------------------------------

The intersite routing is achieved through a number of technologies.  Ashland -> Routes to DataCenter West via a L2 leased dark fiber.  The dark fiber is
100mbps sync connection.

![IntersiteDiagram](/static/intersite-networking.png)

### Ashland Side

The Ashland side of the transacation is a Cat6509E bringing the network in through an interface on our fiber blade.


    interface GigabitEthernet1/24
    description new-afn-uplink
    switchport
    switchport access vlan 3
    switchport trunk encapsulation dot1q
    switchport trunk native vlan 450
    switchport trunk allowed vlan 450,664,687
    switchport mode trunk
    no cdp enable
    spanning-tree portfast edge
    spanning-tree bpdufilter enable
    spanning-tree bpduguard enable
    end

Further the routes to the 172.16.20.0/24 subnet are held in place by a static route ( for now until OSPF and DMVPN can be implemented )

    ip route 172.16.20.0 255.255.255.0 172.16.4.10 tag 20

As all intervlans at OSF use the Cat6509E at Ashland as their gateway of last resort this facilitates intersite.

### Medford Side

On the Medford side of the transaction the L2 lan terminates in an Adtran switch and reaches the Dell-VRTX chassis via a cross connect terminated in gi0/1.

 *     Two devices 1 physical and 1 virtual are involved in the handoff.
 *     Device #1 -- A Dell Embedded Network Management Module
 *     Device #2 -- A Virtualized PFSense firewall


The Dell embedded switch module reachable at 172.16.20.47 and configurable from inside OSF over http uses standard VLAN terminology.  The cables in gig 0/1 and 0/2 bring the two networks into the chassis
as untagged networks which are then in turn tagged to the blades inside the chassis.

Each blade running vmWare ESXi gets the following mapped to it's 1/1 and 2/1 inteface respectively.

All unconfigured ports are simply in VLAN1 the 172.16.20.0/24 subnet to faclitate an easy physical direct connection to the DCWest RFC1918 subnet.


##### VLAN1 Configuration Example

![vlan1](/static/dell-network-vlan1.png)

##### VLAN3 Configuration Example

![vlan3](/static/dell-network-vlan3.png)

##### VLAN687 Configuration Example

![vlan687](/static/dell-network-vlan687.png)


### In vmWare

The VRTX Chassis runs an embedded instance of vSphere server.  The vSphere server is the vmWare virtual appliance flavor based on the SLES jeost server OS.

![vSwitch0](/static/vsphere-network-config.png)

>In the above diagram you can see a single simple vSwitch connected to vmNic0.  vvmNic0 is mapped to the DellVRTX blade interface gig 1/1 and 2/1 respectively.  This is a trunk port with 1 as the native vlan and 3 and 687 as tagged.  Together this plus PFSense facilitate all routing.


### PFSense Configuration

The PFSense configuration is very simple.  Each Public IP in VLAN 3 ( DataCenter West 198.251.115.96/27 ) is mapped to an IP in the 172.16.20.0/24 space using a virtual IP and a 1:1 NAT rule.

The virtual firewall also has three interfaces:

*     LAN : 172.16.20.10
*     WAN : 198.251.115.98
*     OSF ( OPT1 ) : 172.16.4.10

![pfinterfaces](/static/pfinterfaces.png)

The NAT rules below dictate what "private ip" or piece of the 24 bit RFC 1918 allocation will be mapped to the server.

![pfonetoone](/static/pfonetoone.png)

The Firewall rules in the below block from the WAN interface determine what traffic will be passed.

> Note : The firewall rules are pointed at the private IP because they take place post network address translation.

![pfrules](/static/pfrules.png)

> Though not networking related you may note that all HTTP/HTTPs traffic are passed by a single IP.  In this case an NGINX load balancer.




