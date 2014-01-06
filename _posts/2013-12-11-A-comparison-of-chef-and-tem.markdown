---
layout: post
title: A comparison of Chef and TEM
tags: [tivoli endpoint manager, ibm endpoint manager, chef]
---
Build automation has been an interest of mine for a long time (probably about ten years, which shows my age).  I have always
believed that our development environments should be as close to our production environments as possible and the best way to 
do that is to build them from the same code base.

Initially this was done by tools such as ANT that configured the application server and then deployed the application.  Overtime,
this became too clumsy and new tools were developed that built our infrastructure from bare metal up.  There are probably many tools
that cover this, but to my research the three tools that I have found are:

* Chef from OpsCode
* IBM (or Tivoli) End Point Manager
* Puppet
  
Now, to precursor this conversation - I didn't want to spend a lot of cash, but I did manage to play with a trial license of TEM, but
for the other two products I used the open source product rather than the enterprise product.

#### IBM End Point Manager

I tend to run mainly a Linux (Centos and Fedora) environment and was hoping to run IEM on a CentOS host.  From what I can see (and this
is by no means definitive) the code base between IEM on Windows and IEM on Linux is completely different.  The windows installation was
relatively simple but the Linux installation I couldn't get to work at all.  My feeling was the linux is a fairly immature build for this
product.

That said, once the product is installed, it is relatively easy to connect hosts to it.  IEM has a great feature in that you can automatically
group hosts (either for fixlets, groups or tasks) into groups depending on its configuration.  For example, a Centos host can be automatically 
placed into the "Centos" group by the relevance rules.  This allows hosts to be autogrouped as their configuration changes.

The concept of a fixlet is also interesting, as the name would imply the fixlet allows you to fix something on the host.  There is a three step
process with a fixlet:

1. Find hosts for which the fixlet is "relevant".  i.e. which hosts have the vulnerability that needs to be fixed.
1. Execute the fix (i.e. resolve the issue).
1. Ensure that the fixlet has resolved the issue (i.e. is the vulnerability removed).

The core concept of TEM is a push concept where the jobs are "pushed" from the server out to the clients.  My issue with this in TEM is two fold:

1. The client is absolutely enormous and chews a lot of resource on the host.
1. The push sometimes takes a huge amount of time and it is not clear where it is stalled (or not that I can see).  A job that takes a few minutes to 
execute can take a long time to queue and it is diffcult to work out where the bottleneck is.

#### Chef

I compared Chef and Puppet for a long time.  The main drawback of puppet from an open source point of view is the enormous complexity 
with the installation.  Chef seems to me to a much simplified installation.  Once Chef is setup its architecture is very different to IEM.  Firstly,
Chef is a pull framework where the client's pull their configuration from the server.  There is no real concept of a push from the server (although
there is a feature available in the closed source version).  Additionally, Chef has no concept of relevance from what I can see, but it deals with that
in other interesting ways.  Lastly, whereas in IEM the "code" is embedded in the tool, for Chef the code is embeded in a version control system such as git.

Chef uses a kitchen analogy for a lot of its terms (which is ironic because I burn toast).  The work in chef is defined in cookbooks, that contain 
one or more recipes.  These recipes can either be assigned to a host singularly, or via a role.

As an example, I have the following recipes defined in my infrastructure:

* Centos - For configuring Centos servers to the allowable standard.
* Fedora - For configuring Fedora servers to the allowable standard.
* NAS - For configuring my NAS servers (HTTP, SMB etc)
* Jekyll - for configuring my development environment for my blog staging.
* Django on Heroku - for configuring my local django environments for Heroku.
* vmware - for installing and configuring vmware tools.
* SSH - for configuring SSH daemon and some standard SSH keys in my environment.

Now, within chef, you can either assign hosts to a cookbook, or to a role.  The role is literally a group of cookbooks:

For example, my Centos role looks like this:

{% highlight ruby %}
name "centos"
description "CentOS Host"
run_list "recipe[jnrepositories]", "recipe[sshconfig]", "recipe[sshkeys]", "recipe[vmware]", "recipe[cron]"
{% endhighlight %}

Consequently, the process in relation to chef is assigning your hosts to the various recipes and roles to which they are associated with.  This is done via the
chef administration console:

<img src="/assets/2013-12-11-ChefServer.png" class="img-responsive" alt="Chef Server Screenshot" />

#### Chef server push
The reality is that there are sometimes when you want to push configurations from the central server.  I have worked out two methods to do this with the open
source Chef:

1. To ensure your environment is up to date, I call chef client each hour by sticking a reference to the "chef-client" command in the /etc/cron.hourly directory.
This not only ensures that your environment is up-to-date but it allows you to push changes to your environment within an hour of uploading them to your chef server.
1. There is also the ability to run commands on either the whole estate or various sub-sections by sending commands through knife (the command line client for chef).  The 
syntax for doing this is as follows:

{% highlight sh %}
knife ssh "role:centos" "yum update -y" --ssh-user root
{% endhighlight %}

#### Final statements
I wouldn't call myself an expert in either Chef or IEM, but having done the review they are both fine automation toolsets with a different approach to performing
environment automation.  I shall continue to use Chef because it suits my environment and provides less overhead, but in another circumstance I would certainly 
recommend IEM as well.
