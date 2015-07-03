---
title: Huawei HVS Configuration Guide
---
The libStorageMgmt can manage Huawei HVS via SMI-S plugin
(`libstoragemgmt-smis-plugin` package).

Huawei HVS is using embedded SMI-S provider which ran by the storage
array itself. Please contact Huawei support to enable SMI-S feature.

Once done, the URI is:

```
smispy://username@host
smispy+ssl://username@host
# The 'username' is the username of management account of Huawei array.
# The 'host' is the ip/host of management port of Huawei array.
```

For 'SSL error: certificate verify failed' error, it means LSM failed to
verify Huawei SMI-S providers' SSL public keys with system wide PKI/x509.
Please contact Huawei support to setup the self-signed certification.

If you want to ignore the SSL check error which is not suggested, please
add `no_ssl_verify=yes` in your URI, like this:

```
smispy+ssl://username@host?no_ssl_verify=yes
```
