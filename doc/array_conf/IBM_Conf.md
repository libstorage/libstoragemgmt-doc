---
title: IBM DS/XIV/SVC Configuration Guide
---
The libStorageMgmt can manage IBM DS/XIV/SVC via SMI-S plugin
(`libstoragemgmt-smis-plugin` package).

IBM DS/XIV/SVC are using embedded SMI-S provider which ran by the
storage array itself. Please contact IBM support to enable SMI-S
feature.

Once done, the URI is:

```
smispy://username@host
smispy+ssl://username@host
# The 'username' is the username of management account of IBM array.
# The 'host' is the ip/host of management port of IBM array.
```

For 'SSL error: certificate verify failed' error, it means LSM failed to
verify IBM SMI-S providers' SSL public keys with system wide PKI/x509.
Please contact IBM support to setup the self-signed certification.

If you want to ignore the SSL check error which is not suggested, please
add `no_ssl_verify=yes` in your URI, like this:

```
smispy+ssl://username@host?no_ssl_verify=yes
```
