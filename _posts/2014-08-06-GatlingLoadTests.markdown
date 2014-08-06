---
layout: post
title: "Gatling Load Tests"
date: 2014-08-06 11:28:02
---

###Load Testing
--------------------------

We have simulated load across our web farm using a scala based testing framework called [Gatling](http://http://gatling.io/ "Gatling").

###Test Creation
-------------------------

###Running The Tests
-------------------------
From the command line navigate to `$GATLING_HOME/bin` and run `./gatling.sh`

Simulations are recursively searched for within the directory `$GATLING_HOME/user_file/simulations` and presented for selection in a text based menu.

    Choose a simulation number:
         [0] ES4H125u
         [1] ES4H1u
         [2] ES4H250u
         [3] ES4H250ux2
         [4] ES4H250ux4
         [5] ES4H75u
         [6] osfashland.ES3H125u
         [7] osfashland.ES3H1u
         [8] osfashland.ES3H250u
         [9] osfashland.ES3H250ux2
         [10] osfashland.ES3H250ux4
         [11] osfashland.ES3H75u

After selecting the simulation enter an id for the test, the default being the scenario name defined in the test.

    Select simulation id (default is 'es4h125u'). Accepted characters are a-z, A-Z, 0-9, - and _
    EStack4H250u2   
    Select run description (optional)
    
    Simulation ES4H125u started...

Realtime statistics are provided every 10 seconds while the test is running and results are placed a folder in the directory `$GATLING_HOME\results` with a name that matches the standard `<simulationid>-<timestamp>`

###Stitching Multiple Tests
----------------------------

###Drawbacks
--------------------------
