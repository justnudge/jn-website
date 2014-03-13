---
layout: post
title: Mirroring Centos and Fedora Repositories
tags: [linux]
---
If you perform many Linux builds from bare metal, there can be a big advantage in mirroring the yum repositories, the biggest of which is speed.
This is because when you mirror the repository all the files are moved off disks attached to your LAN rather than via Internet.
We synchronize the repositories each night using [Jenkins](http://jenkins-ci.org/) and generally speaking they complete within 30 minutes.
If there is a large release (such as a new version of Fedora) then sometimes the downloads are still happening as we start work in the morning.

Some questions that weren't clear when we started on this process, are what do you synchronize and how much space does it take up.

#### How much diskspace do you need for the mirror?
That question is difficult to answer directly, but we do the following:
1. Only download 64 bit build, i.e.. ignore 32 bit and other platforms.
1. Ignore ISO imagines.
1. Only download the versions that you require, ignore the others.

Hence, our current exclude file for Centos rsync is as follows:

    **/*.iso
    **/isos
    **/i386/**
   
This allows you to support Centos 5 and Centos 6.

For Fedora we use the following exclude file:

    development
    releases/10/**
    releases/11/**
    releases/12/**
    releases/13/**
    releases/14/**
    releases/15/**
    releases/16/**
    releases/17/**
    releases/18/**
    updates/10/**
    updates/11/**
    updates/12/**
    updates/13/**
    updates/14/**
    updates/15/**
    updates/16/**
    updates/17/**
    updates/18/**
    **/i386/**
    **/debug/**
    **/alpha/**
    **/SRPMS/**
    **/*.iso
    **/test/**
    **/source/**
    **/testing/**

Given that the current version of Fedora 20, this means that we currently mirror version 19 and 20.  When 21 comes out I will probably cease to mirror 19.
We also exclude all testing, alpha and debug rpm's.

So the *current* sizes for the repositories are as follows:
* Fedora - ~ 200Gb
* Centos - ~ 30Gb

These allow us to perform PXE based network installations (if you want the ISO's for installation you will need to modify the excludes).

What command do you run to sync the mirror?

This will be dependent on where you are, but we use iiNet as our ISP and they provide an rsync server that contains a whole lot of repositories,
two of which are Fedora and Centos.  Our commands for syncing these repositories are as follows:

    rsync --archive --verbose --delete --delete-delay --delay-updates mirror.internode.net::centos /opt/mirror/centos --exclude-from /etc/excludes/centos
    
and for Fedora:

    rsync --archive --verbose --delete --delete-delay --delay-updates  mirror.internode.net::fedora /opt/mirror/fedora --exclude-from /etc/excludes/fedora
    
The advantage of delaying deletes and delaying updates is that it keeps the mirror in sync (much like wrapping multiple database transactions into a 
single commit).  Failure to do this might mean that during a build (or an update) the mirror could be inconsistent.

We haven't found a lot of documentation around that details this so we have detailed here in the hopes that it may help some people.
