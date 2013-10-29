---
layout: post
title: Setting up a PXE boot server with CentOS and DNSMasq
tags: [pxe, dnsmasq, linux]
---
I am building a lot of temporary hosts at the moment and being able to install them as quickly as possible is always good.  So I have decided to 
install a PXE server that will allow my installations to install without human intervention.

To setup this you need an existing host that contains a mirror of Centos (you can get away without a mirror but syncing a mirror is so much faster than 
going out onto the internet to download all the necessary patches that may be required). If you do setup a mirror allocate about 150Gb of Diskspace to 
the mirror and sync it every evening using rsync.

On the host execute the following command:

{% highlight sh %}
yum install syslinux tftp tftp-server -y
{% endhighlight %}

Once the server is installed execute the following command:

{% highlight sh %}
chkconfig xinetd on
service xinetd restart
{% endhighlight %}

To copy the files to enable the clients to boot execute the following commands:

{% highlight sh %}
cd /var/lib/tftpboot
cp /usr/share/syslinux/menu.c32 .
cp /usr/share/syslinux/pxelinux.0 .
cp /opt/mirror/centos/6.3/os/x86_64/images/pxeboot/initrd.img .
cp /opt/mirror/centos/6.3/os/x86_64/images/pxeboot/vmlinuz .
{% endhighlight %}

NOTE: TFTP does not seem to work with symlinks so the files need to be copied.

When the server boots it will attempt to use it’s MAC address to determine what boot configuration it would use and will fall back onto a configration called “default”:

{% highlight sh %}
mkdir -p /var/lib/tftpboot/pxelinux.cfg
vi /var/lib/tftpboot/pxelinux.cfg/default
Enter the following information into the file and save it:

timeout 100
default menu.c32
 
menu title ########## JustNudge Boot Menu ##########
label 1
   menu label ^1) Install CentOS 6
   kernel vmlinuz
   append initrd=initrd.img devfs=nomount ks=http://nas.justnudge.com/centos6.ks
 
label 2
   menu label ^2) Boot from local drive
   localboot
{% endhighlight %}

The above example will automatically install Centos using a kickstart file that configures it as per our build standards.

To ensure that the above is picked up, add the following line to your dnsmasq configuration file:

{% highlight sh %}
dhcp-boot=pxelinux.0,nas.justnudge.com,192.168.1.50
{% endhighlight %}

Where the PXE server is nas.justnudge.com with an IP address of 192.168.1.50.

When started the VM will look like the following:

![PXE boot screenshot]({{ site.url }}/assets/pxe.png)
