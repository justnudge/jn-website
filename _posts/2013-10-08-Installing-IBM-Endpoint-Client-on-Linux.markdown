---
layout: post
title: Installing IBM Endpoint Client on Linux
tags: [tivoli endpoint manager, ibm endpoint manager, linux]
permalink: /2013/02/using-the-google-authenticator-with-centos-6/
---
I have been investigating IBM End Point, which appears at first gance to be a fantasic tool.  The help for Linux clients appears to be a little
light (or am I missing something?) but in order to install the End Point Client onto Linux you run the following commands:

{% highlight sh %}
mkdir ~/bigfix-client-tmp
mkdir -p /etc/opt/BESClient/
cd ~/bigfix-client-tmp
wget http://nas.justnudge.com/software/bigfix/BESAgent-current.x86_64.rpm
wget http://nas.justnudge.com/software/bigfix/masthead.afxm
mv masthead.afxm /etc/opt/BESClient/actionsite.afxm
rpm -ivh BESAgent-current.x86_64.rpm
cd ~
rm -rf ~/bigfix-client-tmp
chkconfig besclient on
service besclient start
{% endhighlight %}

I have placed the masthead and actionsite files onto my NAS for ease of use, but they are located in the following location on a standard
IEM installation:

- C:\Program Files (x86)\BigFix Enterprise\BES Server\actionsite.afxm
- C:\Program Files (x86)\BigFix Enterprise\BES Server\BESReportsData\swiftsure.justnudge.com\masthead.afxm

Note, my IEM server is called swiftsure.justnudge.com.
