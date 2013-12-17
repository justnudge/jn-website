---
layout: post
title: Adding a new node in chef is easy
tags: [chef]
---
I am currently playing around with chef.  As I mentioned earlier, the command line client for controlling Chef is
called Knife (appropriately enough given the cooking theme).  To add a new node to the configuration you run the following command:

    C:\Users\mransley\workspace\jn-infrastructure>knife bootstrap cobalt.justnudge.com
    Bootstrapping Chef on cobalt.justnudge.com
    Failed to authenticate root - trying password auth
    Enter your password:
    cobalt.justnudge.com --2013-12-17 12:12:43--  https://www.opscode.com/chef/install.sh
    cobalt.justnudge.com Resolving www.opscode.com (www.opscode.com)... 184.106.28.90
    cobalt.justnudge.com Connecting to www.opscode.com (www.opscode.com)|184.106.28.90|:443... connected.
    cobalt.justnudge.com HTTP request sent, awaiting response... 200 OK
    cobalt.justnudge.com Length: 14101 (14K) [application/x-sh]
    cobalt.justnudge.com Saving to: ???STDOUT???
    cobalt.justnudge.com
    100%[======================================>] 14,101      --.-K/s   in 0s
    cobalt.justnudge.com
    cobalt.justnudge.com 2013-12-17 12:12:45 (325 MB/s) - written to stdout [14101/14101]
    cobalt.justnudge.com
    cobalt.justnudge.com Downloading Chef 11.8.0 for el...
    cobalt.justnudge.com downloading https://www.opscode.com/chef/metadata?v=11.8.0&prerelease=false&p=el&pv=6&m=x86_64
    cobalt.justnudge.com   to file /tmp/install.sh.765/metadata.txt
    cobalt.justnudge.com trying wget...
    cobalt.justnudge.com url        https://opscode-omnibus-packages.s3.amazonaws.com/el/6/x86_64/chef-11.8.0-1.el6.x86_64.rpm
    cobalt.justnudge.com md5        8f72d94dffd75c3f79dc0fcd457e7f11
    cobalt.justnudge.com sha256     1a1e3df541240dcf5da1d128b1a4341224db5a2e1dee83b3f0f5d41e0aadb2a3
    cobalt.justnudge.com downloaded metadata file looks valid...
    cobalt.justnudge.com downloading https://opscode-omnibus-packages.s3.amazonaws.com/el/6/x86_64/chef-11.8.0-1.el6.x86_64.rpm
    cobalt.justnudge.com   to file /tmp/install.sh.765/chef-11.8.0.x86_64.rpm
    cobalt.justnudge.com trying wget...
    cobalt.justnudge.com Checksum compare with sha256sum succeeded.
    cobalt.justnudge.com Installing Chef 11.8.0
    cobalt.justnudge.com installing with rpm...
    cobalt.justnudge.com warning: /tmp/install.sh.765/chef-11.8.0.x86_64.rpm: Header
     V4 DSA/SHA1 Signature, key ID 83ef826a: NOKEY
    cobalt.justnudge.com Preparing...
    ################################# [100%]
    cobalt.justnudge.com Updating / installing...
    cobalt.justnudge.com    1:chef-11.8.0-1.el6
    ################################# [100%]
    cobalt.justnudge.com Thank you for installing Chef!
    cobalt.justnudge.com Starting Chef Client, version 11.8.0←[0m
    cobalt.justnudge.com Creating a new client identity for cobalt.justnudge.com using the validator key.←[0m
    cobalt.justnudge.com resolving cookbooks for run list: []←[0m
    cobalt.justnudge.com Synchronizing Cookbooks:←[0m
    cobalt.justnudge.com Compiling Cookbooks...←[0m
    cobalt.justnudge.com [2013-12-17T12:13:29+10:00] WARN: Node cobalt.justnudge.com  has an empty run list.
    cobalt.justnudge.com Converging 0 resources←[0m
    cobalt.justnudge.com Chef Client finished, 0 resources updated←[0m

That method of connectivity certainly appears simpler than IEM based on my experience.