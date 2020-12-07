---
title: NetApp ONTAP Configuration Guide
---

LibstoragMgmt previously offered two ways to management NetApp ONTAP
storage array:

* [Native NetApp ONTAP SDK. **(Removed)**][1]
* [NetApp SMI-S provider][2]

The NetApp 7-mode plugin has been removed as NetApp has deprecated the API. We
are leaving the documentation here in case you are using a very old array and
are using older versions of libStorageMgmt.  The SMI-S method offers a
subset of the functionality the native API provided.

### Native NetApp ONTAP SDK. **(Removed)**

You need to install the package `libstoragemgmt-ontap-plugin`
if you didn't install using the distribution tarball.

Please contact NetApp support to enable http/https management
interface or use this command **at your own risk**:

```
options httpd.enable on
options httpd.admin.enable on
options httpd.admin.ssl.enable on
```

The URI would be:

```
ontap://username@host
ontap+ssl://username@host
# The 'username' is the management username of NetApp ONTAP.
# The 'host' is the ip/hostname of ONTAP Filer management interface.
```

### NetApp ONTAP SMI-S provider

NetApp is using proxy SMI-S provider. You need to install the
package `libstoragemgmt-smis-plugin` if you didn't install using the
distribution tarball.

Please contact NetApp support to install and
configure a SMI-S provider service on your server. The URI would be:

```
    smispy://username@host
    smispy+ssl://username@host
    # The 'username' is the user name of server you installed SMI-S
    # providers on.
    # The 'host' is the ip/hostname of server you installed SMI-S
    # providers on.
```

For 'SSL error: certificate verify failed' error, it means
libstoragemgmt failed to verify NetApp SMI-S providers' SSL public keys
with system wide PKI/x509.
Please contact NetApp support to setup the self-signed certification.

If you want to ignore the SSL check error which is not suggested, please
add `no_ssl_verify=yes` in your URI, like this:

```
smispy+ssl://username@host?no_ssl_verify=yes
```

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

* Download SMI-S provider from [NetApp website][3]

* Execute these commands to install required rpms (if on x86_64)

    ```bash
    sudo yum install -y ncompress glibc.i686 tog-pegasus-libs.i686 \
        libzip.i686
    ```

* Install SMI-S provider (Follow EMC documents if found):

    ```bash
    tar xf smisagent-5-0.tar
    sudo ./install_smisproxy
    ```

* SMI-S provider configurate:

    ```bash
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
    ```

* For autostart SMI-S provider, please add these lines to your
  /etc/rc.local:

    ```bash
    export PEGASUS_ROOT=/usr/ontap/smis/pegasus
    export PEGASUS_HOME=/usr/ontap/smis/pegasus
    export PEGASUS_PLATFORM_=LINUX_IX86_GNU
    export PATH=/usr/bin:$PATH:/usr/ontap/smis/pegasus/bin
    export LD_LIBRARY_PATH=/usr/ontap/smis/pegasus/lib:$LD_LIBRARY_PATH
    export LD_LIBRARY_PATH=/usr/lib:/usr/local/lib:$LD_LIBRARY_PATH
    smis cimserver start
    ```

Above steps were tested on:

* `RHEL 6.5 x86_64`
* `NetApp SMI-S Provider 5.0 (smisagent-5-0.tar)`

[1]: #native-netapp-ontap-sdk-preferred
[2]: #netapp-ontap-smi-s-provider
[3]: http://support.netapp.com/download/defs/smis/Linux.shtml
