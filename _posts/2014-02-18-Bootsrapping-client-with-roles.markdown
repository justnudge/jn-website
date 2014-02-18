---
layout: post
title: Bootstrapping a chef client with roles
tags: [chef]
---
I am slowly playing around more and more with chef and the more I see of it the more I like.  One of things that you can do is
assign roles and recipes when a client is boostrapped.  This means that its default configuration is achieved during bootstrapping.  To
do this run the following command:

    knife bootstrap oxygen.justnudge.com -r 'role[jndefault]'
    
Where the host is called "oxygen.justnudge.com" and we are assigning the role "jndefault" to it.