---
title: General SMI-S provider Configuration Guide
---
Many storage vendor offers SMI-S service for storage array management.
The libStorageMgmt could use SMI-S plugin
(`libstoragemgmt-smis-plugin` package) to interact with them.

To get full support, SNIA SMI-S 1.4 or later version is required.

Please contact storage vendor support for enabling SMI-S service.
Once done, the URI is:

```
smispy://username@host:<port>
smispy+ssl://username@host:<port>
# The 'username' is the username of management account of
# SMI-S service.
# The 'host' is the ip/host of management port of SMI-S
# service.
# By default, the non-ssl connection will use 5988 port, and 5989 for
# ssl connection. If not, specify it.
```

For 'SSL error: certificate verify failed' error, it means LSM failed to
verify your SMI-S providers' SSL public keys with system wide PKI/x509.
Please contact SMI-S vendor's support to setup the self-signed
certification.

If you want to ignore the SSL check error which is not suggested, please
add 'no_ssl_verify=yes' in your URI, like this:

```
smispy+ssl://username@host?no_ssl_verify=yes
```
