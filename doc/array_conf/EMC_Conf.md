---
title: EMC CX/VNX/VMAX Configuration Guide
---
The libStorageMgmt can manage EMC CX/VNX/VMAX via SMI-S plugin
(`libstoragemgmt-smis-plugin` package).

EMC is providing a proxy SMI-S provider for storage array management.

Please contact EMC Support to install and configure a SMI-S provider
service on your server for your storage arrays.

Once done, you could use one of these URIs in LibStorageMgmt:

```
smispy://username@host
smispy+ssl://username@host
# The 'username' is the username used in EMC SMI-S provider.
# The default 'username' is 'admin' and the default password
# is '#1Password'
```

You can also add "&system=<system_id>" to filter out unneeded storage
arrays like this:

```
smispy://username@host?system=<system_id>
# The 'system_id' could be found via systems() call or
# 'lsmcli list --type SYSTEMS' command.
```

For 'SSL error: certificate verify failed' error, it means
libstoragemgmt failed to verify EMC SMI-S providers' SSL public keys
with system wide PKI/x509.
Please contact EMC support to setup the self-signed certification.

If you want to ignore the SSL check error which is not suggested, please
add `no_ssl_verify=yes` in your URI, like this:

```
smispy+ssl://username@host?no_ssl_verify=yes
```

<p style="color: red">
<b>
The following are the steps used by ourselves on EMC SMI-S provider
<br>
installation and configuration.
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact EMC Support instead if are working on production system.
</b>
</p>

* Download SMI-S provider from support.emc.com

* Execute these commands to install required rpms (if on x86_64)

    ```bash
    yum install glibc.i686 libgcc.i686
    ```

* Install SMI-S provider (Follow EMC documents if found):

    ```bash
    ./se76012_install.sh -install -smi
    ```

* This command will start EMC SMI-S provider if not started:

    ```bash
    /opt/emc/ECIM/ECOM/bin/ECOM -d
    ```

* Add Storage Array login information to EMC SMI-S provider:

    ```bash
    /opt/emc/ECIM/ECOM/bin/TestSmiProvider addsys
    # Follow the instruction of 'addsys'.
    # You need to input both SPA and SPB's management IP address.
    # During our test, 'addsys' will fail if only one IP defined.
    ```

* If you want to remove any array from EMC SMI-S provider,
  but have no idea about the path requested by `rmsys`.
  Try command `tv`.

* By default, the password will expire in 30 days, you can change
  that rule via `https://<smis_server_ip>:5989/ECOMConfig`. You can
  also change the password there if you want it expire every 30 days.

Above steps were tested on:

* `RHEL 6.4 x86_64`
* SMI-S Provider 4.6.0.3 for SMI-S 1.5 for Linux
    * `se76012-Linux-i386-SMI.tar.gz`
    * `symcli-smi64-7.6.0.1707-12.3.x86_64`
