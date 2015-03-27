---
title: NetApp ONTAP Configuration Guide
---

LibstoragMgmt could use these two ways to management NetApp ONTAP storage array:

* Navtive NetApp ONTAP SDK.(Preferred)
  You need to install the package libstoragemgmt-ontap-plugin.noarch if you didn't install
  using the distribution tarball.

  Please contact NetApp support to enable http/https management interface or use this
  command on your own risk:

        options httpd.enable on
        options httpd.admin.enable on
  The URI would be:

        ontap://username@host
        ontap://username@host
        # The 'username' is the management username of NetApp ONTAP.
        # The 'host' is the ip/hostname of ONTAP Filer management interface.

* NetApp ONTAP SMI-S provider.
  NetApp is using proxy SMI-S provider.  You need to install the package libstoragemgmt-
  smis-plugin.noarch if you didn't install using the distribution tarball.
  Please contact NetApp support to install and configure a SMI-S provider service on
  your server. The URI would be:

        smispy://username@host:5988?namespace=root/ontap
        smispy+ssl://username@host:5989?namespace=root/ontap
        # The 'username' is the user name of server you installed SMI-S providers on.
        # The 'host' is the ip/hostname of server you installed SMI-S providers on.

<p style="color: red">
<b>
The following are the steps used by ourselves on NetApp SMI-S provider
<br>
installation and configuration.
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact NetApp Support instead if are working on production system.
</b>
</p>

1. Download SMI-S provider from [NetApp website](http://support.netapp.com/download/defs/smis/Linux.shtml)

* Execute these commands to install required rpms (if on x86_64)

        :::bash
        sudo yum install -y ncompress glibc.i686 tog-pegasus-libs.i686 libzip.i686

* Install SMI-S provider (Follow EMC documents if found):

        :::bash
        tar xf smisagent-5-0.tar
        sudo ./install_smisproxy

* SMI-S provider configurate:

        :::bash
        su -    # root login is required
        export PEGASUS_ROOT=/usr/ontap/smis/pegasus
        export PEGASUS_HOME=/usr/ontap/smis/pegasus
        export PEGASUS_PLATFORM_=LINUX_IX86_GNU
        export PATH=/usr/bin:$PATH:/usr/ontap/smis/pegasus/bin
        export LD_LIBRARY_PATH=/usr/ontap/smis/pegasus/lib:$LD_LIBRARY_PATH
        export LD_LIBRARY_PATH=/usr/lib:/usr/local/lib:$LD_LIBRARY_PATH
        smis cimserver start
        smis addsecure <netapp_filer_ip> <user> <passwd>
        cimconfig -p -s enableAuthentication=true
        cimuser -a -u pegasus -w pegasus
        # Change the 'pegasus' to your favorite SMI-S user/pass

* For autostart SMI-S provider, please add these lines to your /etc/rc.local:

        :::bash
        export PEGASUS_ROOT=/usr/ontap/smis/pegasus
        export PEGASUS_HOME=/usr/ontap/smis/pegasus
        export PEGASUS_PLATFORM_=LINUX_IX86_GNU
        export PATH=/usr/bin:$PATH:/usr/ontap/smis/pegasus/bin
        export LD_LIBRARY_PATH=/usr/ontap/smis/pegasus/lib:$LD_LIBRARY_PATH
        export LD_LIBRARY_PATH=/usr/lib:/usr/local/lib:$LD_LIBRARY_PATH
        smis cimserver start

Above steps were tested on:

* RHEL 6.5 x86_64
* NetApp SMI-S Provider 5.0 (smisagent-5-0.tar)
