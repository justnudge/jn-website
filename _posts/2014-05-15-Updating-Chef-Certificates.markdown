---
layout: post
title: Updating Chef Certificates
tags: [chef]
---
I ran into a problem in my chef environment in that I was using the original certificate that it was installed with and
I was getting some trust issues when using the infrastructure.  So I decided to use a signed certificate from my internal
CA.  The following is a quick overview of getting that going.

Assuming that you have the public and private key.  Log into your chef server and go to the following directory:

    /var/opt/chef-server/nginx/ca

Replace the yourhostname.example.com.key with your private key and yourhostname.example.com.crt with your public key.

Reconfigure the Chef Server by running:

<kdb>chef-server-ctl reconfigure</kdb>

And then restart your server by running:

    chef-server-ctl stop
    chef-server-ctl start

And your Chef server should now be using your new certificate.