---
layout: post
title: Bootstrapping OSX with Chef
tags: [chef]
---
I have already moved much of my development infrastructure to Chef and thought
that I would save a lot of time and learn more about Chef by moving my workstation
configuration to Chef as well.  I run a MacBook Pro and in order to bootstrap it
you run the following command:

    knife bootstrap titanium.justnudge.com -x mransley --sudo

You need to use the sudo command together with the username (-x) because Mac OSX
doesn't really use the root account that much.

    titanium:Desktop mransley$ knife bootstrap titanium.justnudge.com -x mransley --sudo
    Connecting to titanium.justnudge.com
    Password:
    titanium.justnudge.com Starting first Chef Client run...
    titanium.justnudge.com Starting Chef Client, version 11.12.8
    titanium.justnudge.com Creating a new client identity for titanium.justnudge.com using the validator key.
    titanium.justnudge.com resolving cookbooks for run list: []
    titanium.justnudge.com Synchronizing Cookbooks:
    titanium.justnudge.com Compiling Cookbooks...
    titanium.justnudge.com [2014-08-04T16:21:36+10:00] WARN: Node titanium.justnudge.com has an empty run list.
    titanium.justnudge.com Converging 0 resources
    titanium.justnudge.com
    titanium.justnudge.com Running handlers:
    titanium.justnudge.com Running handlers complete
    titanium.justnudge.com
    titanium.justnudge.com Chef Client finished, 0/0 resources updated in 9.399649 seconds
    titanium:Desktop mransley$

And the OSX host is successfully bootstrapped.
