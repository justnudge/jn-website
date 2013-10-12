---
layout: post
title: Installation Issues with IBM End Point Manager
tags: [iem, tem, ibm endpoint manager]
---

I am performing a clean installing of IBM End Point Manager (the old name for the product was Tivoli End Point Manager) and during the installation I received the following error:

    [root@swiftsure ServerInstaller_9.0.649.0-rhel]# ./install.sh
    Welcome to the installation of IBM Endpoint Manager version 9.0.649.0
    ####################
    OS Prerequisites Check
      - OS Distribution and Architecture, Check passed: 'true'
      - OS Version, Check passed: 'true'
      - OS Libraries, Check passed: 'false'
    One or more of the above prerequisite checks failed.
    [1]  Ignore Warning and proceed with the installation
    [2]  Exit from the installation
    Choose one of the options above or press <Enter> to accept the default: [2]

Obviously the OS Libraries, Check passed: 'false' was a bit of worry but where to find out what libraries were missing. I searched online to no-avail and resorted to reading through the installation scripts. It turns out that for Version 9.0 the following pre-requisites are required: 

- pam
- cyrus-sasl-lib
- libstdc++

I personally was missing i686 versions of the file (even though I am running a x64 bit installation) and when I added them in this allowed the installation to proceed.