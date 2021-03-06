---
layout: post
title: Using Google Authenticator with CentOS 6
tags: [linux]
---
We have been doing some work recently with Amazon Web Services and noticed that it now supports using Google Authenticator for two 
factor authentication.  For those that don’t know, the Google Authenticator is an application that you install onto your Android or 
iPhone which acts like an RSA token, providing a random number that changes every 30 seconds.

When I saw that Amazon had integrated it into AWS it got me thinking that we could use it to secure some of our perimeter Centos hosts 
using an SSH PAM module.  A little bit of searching showed that this was possible and this post details how it was done.

Installation
---
Install Centos 6.2.

{% highlight sh %}
Run “yum install make pam-devel -y”
{% endhighlight %}

Download the source for the PAM module here.

Unpack the installation running the following command:

{% highlight sh %}
[root@JNC6NET0004 install]# tar xfvj libpam-google-authenticator-1.0-source.tar.bz2
libpam-google-authenticator-1.0/base32.c
libpam-google-authenticator-1.0/demo.c
libpam-google-authenticator-1.0/google-authenticator.c
libpam-google-authenticator-1.0/hmac.c
libpam-google-authenticator-1.0/pam_google_authenticator.c
libpam-google-authenticator-1.0/pam_google_authenticator_unittest.c
libpam-google-authenticator-1.0/sha1.c
libpam-google-authenticator-1.0/base32.h
libpam-google-authenticator-1.0/hmac.h
libpam-google-authenticator-1.0/sha1.h
libpam-google-authenticator-1.0/totp.html
libpam-google-authenticator-1.0/Makefile
libpam-google-authenticator-1.0/FILEFORMAT
libpam-google-authenticator-1.0/README
libpam-google-authenticator-1.0/utc-time/
libpam-google-authenticator-1.0/utc-time/app.yaml
libpam-google-authenticator-1.0/utc-time/utc-time.py
{% endhighlight %}

Change to the extracted directory and execute the command “make install”:

{% highlight sh %}
[root@JNC6NET0004 libpam-google-authenticator-1.0]# make install
gcc --std=gnu99 -Wall -O2 -g -fPIC -c  -fvisibility=hidden  -o pam_google_authenticator.o pam_google_authenticator.c
gcc -shared -g   -o pam_google_authenticator.so pam_google_authenticator.o base32.o hmac.o sha1.o -lpam
gcc --std=gnu99 -Wall -O2 -g -fPIC -c  -fvisibility=hidden  -o demo.o demo.c
gcc -DDEMO --std=gnu99 -Wall -O2 -g -fPIC -c  -fvisibility=hidden  -o pam_google_authenticator_demo.o pam_google_authenticator.c
gcc -g   -rdynamic -o demo demo.o pam_google_authenticator_demo.o base32.o hmac.o sha1.o  -ldl
gcc -DTESTING --std=gnu99 -Wall -O2 -g -fPIC -c  -fvisibility=hidden        \
              -o pam_google_authenticator_testing.o pam_google_authenticator.c
gcc -shared -g   -o pam_google_authenticator_testing.so pam_google_authenticator_testing.o base32.o hmac.o sha1.o -lpam
gcc --std=gnu99 -Wall -O2 -g -fPIC -c  -fvisibility=hidden  -o pam_google_authenticator_unittest.o pam_google_authenticator_unittest.c
gcc -g   -rdynamic -o pam_google_authenticator_unittest pam_google_authenticator_unittest.o base32.o hmac.o sha1.o -lc  -ldl
cp pam_google_authenticator.so /lib64/security
cp google-authenticator /usr/local/bin
[root@JNC6NET0004 libpam-google-authenticator-1.0]#
{% endhighlight %}

Installing the PAM module
---
Backup the file “/etc/pam.d/sshd” and add the following lines to it:

{% highlight sh %}
auth required pam_google_authenticator.so
{% endhighlight %}

Backup the file “/etc/ssh/sshd_config” and ensure the following lines are present:

{% highlight sh %}
PermitRootLogin no
ChallengeResponseAuthentication yes
{% endhighlight %}

Once these changes have been made restart SSH by executing the following command:

{% highlight sh %}
service sshd restart
{% endhighlight %}

Setting up the key for a user
---

su to the user that you want generate the token for and execute the google authenticator command::

{% highlight sh %}
[root@JNC6NET0004 libpam-google-authenticator-1.0]# su - mransley
[mransley@JNC6NET0004 ~]$
[mransley@JNC6NET0004 ~]$ google-authenticator
Do you want authentication tokens to be time-based (y/n) y
https://www.google.com/chart?chs=200x200&amp;chld=M|0&amp;cht=qr&amp;chl=otpauth://totp/mransley@JNC6NET0004%3Fsecret%3DXXXXXX
Your new secret key is: XXXXXXXXXXX
Your verification code is XXXXXXX
Your emergency scratch codes are:
  XXXXXXXX
  XXXXXXXX
  XXXXXXXX
  XXXXXXXX
  XXXXXXXX
 
Do you want me to update your "/home/mransley/.google_authenticator" file (y/n) y
 
Do you want to disallow multiple uses of the same authentication
token? This restricts you to one login about every 30s, but it increases
your chances to notice or even prevent man-in-the-middle attacks (y/n) y
 
By default, tokens are good for 30 seconds and in order to compensate for
possible time-skew between the client and the server, we allow an extra
token before and after the current time. If you experience problems with poor
time synchronization, you can increase the window from its default
size of 1:30min to about 4min. Do you want to do so (y/n) y
 
If the computer that you are logging into is not hardened against brute-force
login attempts, you can enable rate-limiting for the authentication module.
By default, this limits attackers to no more than 3 login attempts every 30s.
Do you want to enable rate-limiting (y/n) y
[mransley@JNC6NET0004 ~]$
{% endhighlight %}

You will notice above that it displayed a secret key and a URL, open the URL and it will show you 3D barcode.

Open the authenticator application and click the “Scan Barcode” button and scan the barcode from your screen.

![Google Authenticator Scan Barcode]({{ site.url }}/assets/GoogleAuthenticator1.png)

You should then see the counter for the application.

![Google Authenticator Counter]({{ site.url }}/assets/GoogleAuthenticator2.png)

Testing
---
Open up a new SSH terminal (such as Putty) to the host and login as the user that created the token above.

Enter the verification code from the Google authenticator.

Enter the user’s password.

All being well you should be able to login as shown below:

![Google Authenticator Putty]({{ site.url }}/assets/GoogleAuthenticatorPutty.png)