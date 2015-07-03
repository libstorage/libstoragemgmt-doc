---
title: NetApp E-Series Configuration
---
The libStorageMgmt can manage NetApp E-Series via SMI-S plugin
(`libstoragemgmt-smis-plugin` package).

NetApp E-Series is using proxy SMI-S provider which require install
NetApp E-Series SMI-S provider software on a server.
Please contact NetApp support for more detail.

Once done, the URI is:

```
smispy://username@host?namespace=root/LsiArray13
smispy+ssl://username@host?namespace=root/LsiArray13
# The 'username' is the username set in SMI-S provider.
# The 'host' is the ip/host of SMI-S provider server.
```

For 'SSL error: certificate verify failed' error, it means LSM failed to
verify NetApp-E SMI-S providers' SSL public keys with system wide
PKI/x509.
Please contact NetApp support to setup the self-signed certification.

If you want to ignore the SSL check error which is not suggested, please
add `no_ssl_verify=yes` in your URI, like this:

```
smispy+ssl://username@host?no_ssl_verify=yes
```

<p style="color: red">
<b>
The following are the steps used by ourselves on NetApp-E SMI-S provider
<br>
installation and configuration.
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact NetApp Support instead if are working on production system.
</b>
</p>

* Download NetApp-E SMI-S software from NetApp website.

* Install NetApp-E SMI-S service:

    ```bash
    chmod +x SMIA-LINUXX64-11.10.0A00.0002.bin
    sudo ./SMIA-LINUXX64-11.10.0A00.0002.bin
    ```

* Add IP address of NetApp-E to configuration files:

    ```bash
    echo '10.256.261.309' >> \
        /opt/netapp/pegasus/providers/array/ArrayHosts.txt
    echo '10.256.261.310' >> \
        /opt/netapp/pegasus/providers/array/ArrayHosts.txt
    ```

* Add these lines into `/etc/rc.local` to start SMI-S daemon on boot:

    ```bash
    export PEGASUS_HOME=/opt/netapp/pegasus
    export PEGASUS_ROOT=/opt/netapp/pegasus
    export PATH=/opt/netapp/pegasus/bin:$PATH
    export LD_LIBRARY_PATH=/opt/netapp/pegasus/lib:/opt/netapp/openssl/lib:/opt/netapp/openslp/lib:$LD_LIBRARY_PATH
    cimserver
    ```

Above steps were tested on:

* `RHEL 6.5 x86_64`
* `SMIA-LINUXX64-11.10.0A00.0002.bin`
