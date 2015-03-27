---
title: LSI MegaRAID Configuration Guide
---
LSI is providing SMI-S provider installed on server with MegaRAId cards.
Please contact LSI support to install and configure a SMI-S provider on
your MegaRAID equipped server.

For libStorageMgmt you will need to install the
libstoragemgmt-smis-plugin.noarch package if you didn't install using the
distribution tarball.

Once done, the URI will be:

        smispy+ssl://username@host?namespace=root/LsiMr13
        # The 'username' is the neither root, pegasus or other account
        # has privilege to access open-pegasus(tog-pegasus) daemon.
        # The 'host' is ip/hostname of your MegaRAID equipped server.

For 'SSL error: certificate verify failed' error, it means LSM failed to
verify LSI SMI-S providers' SSL public keys with system wide PKI/x509.
Please contact LSI support to setup the self-signed certification.

If you want to ignore the SSL check error which is not suggested, please
add 'no_ssl_verify=yes' in your URI, like this:

        smispy+ssl://username@host?namespace=root/LsiMr13&no_ssl_verify=yes

<p style="color: red">
<b>
The following are the steps used by ourselves on LSI SMI-S provider
<br>
installation and configuration.
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact LSI Support instead if are working on production system.
</b>
</p>

* Download SMI-S provider from LSI website.
* Execute these commands to install required rpms:

        :::bash
        yum install tog-pegasus

* Change SELinux to permissive mode.
  Follow Red Hat document if want to disable SELinux permanent:

        :::bash
        setenforce 0
        # For security concern of disabling SELinux,
        # please contact Red Hat support or LSI support.

* Install SMI-S provider (Follow LSI documents if found):

        :::bash
        yum install Lib_Utils-1.00-09.noarch.rpm
        yum install lsi_mr_hhr-00.38.0003-rhel6.x86_64.rpm

* Recompule MOF files:

        :::bash
        /opt/lsi/mof/compile_mofs.sh

* Restart tog-pegasus:

        :::bash
        service tog-pegasus restart
        # Daemon tog-pegasus is providing http/https
        # access of LSI SMI-S provider.

* If you want LSI SMI-S provider CIM servier start on boot.
  Make sure you change SELinux to permissive or disable mode.
  Daemon 'tog-pegasus' need configured as on boot also.


Above steps were tested on:

* RHEL 6.4 x86_64
* Lib_Utils-1.00-09.noarch
* lsi_mr_hhr-00.39.0003-rhel6.x86_64
