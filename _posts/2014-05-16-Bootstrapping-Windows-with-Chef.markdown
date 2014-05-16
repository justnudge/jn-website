---
layout: post
title: Bootstrapping Windows with Chef
tags: [chef]
---
I will admit that generally I work with Linux hosts (generally Fedora and RHEL) but sometimes I also work with Windows hosts and would
like to integrate them into my Chef environment.  The official instructions for performing this task is 
<a href="http://docs.opscode.com/plugin_knife_windows.html">here</a>, but the following are my notes.

The first step to doing this is to install the knife-windows plugin on your Chef Server:

    cd /opt/chef-server/embedded/bin
    ./gem install knife-windows
    
Note: to make this work I needed to install some additional libraries above and beyond the libraries that I tend to use in my Chef Server
(which runs Fedora):

    yum install gcc-c++ patch
    
You then need to repeat the installation on the workstation that you run knife from (if that is different from your Chef Server).
    
For the next step, on the windows host you need to install the Windows Management Framework Core Package, available at 
<a href="http://support.microsoft.com/kb/968930">here</a> for most platforms.  This is done by downloading and installing the appropriate
binary for your operating system.

__Note__ The installation of the Windows Management Framework Core Package requires .Net Framework 2.0 SP1, so depending on your operating system
you may need to install that prior to the installation of the Windows Management Framework Core Package.  To update the .Net Framework you can install
the .Net Framework 3.5 Service Pack 1 which includes the necessary patch for the .Net Framework 2.0.

Once that is installed you need to configure the individual windows host.  This is done running the following command.

    winrm quickconfig -q

The output of this command is as follows:

    WinRM already is set up to receive requests on this machine.
    WinRM is not set up to allow remote access to this machine for management.
    The following changes must be made:

    Create a WinRM listener on HTTP://* to accept WS-Man requests to any IP on this machine.
    Enable the WinRM firewall exception.

    WinRM has been updated for remote management.

    Created a WinRM listener on HTTP://* to accept WS-Man requests to any IP on this machine.
    WinRM firewall exception enabled.

Additionally, run some additional commands to configure the Remote Management:

    winrm set winrm/config/winrs @{MaxMemoryPerShellMB="300"}
    winrm set winrm/config @{MaxTimeoutms="1800000"}
    winrm set winrm/config/service @{AllowUnencrypted="true"}
    winrm set winrm/config/service/auth @{Basic="true"}
    netsh advfirewall firewall set rule name="Windows Remote Management (HTTP-In)" profile=public protocol=tcp localport=5985 remoteip=localsubnet new remoteip=any
    
Once this has been done you are ready to bootstrap the host into Chef, this is done by running the following command:

    knife bootstrap windows winrm <hostname> -x <username>
    
Where hostname is the host to bootstrap and username is the usernmae to use to connect to the host.
    
The command weaves its magic and when it is finished you have a newly bootstraped windows host:

<img src="/assets/chef-server-windows.png" class="img-responsive" alt="Chef Server with Windows Host" />

    