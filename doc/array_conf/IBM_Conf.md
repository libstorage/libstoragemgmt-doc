---
title: IBM DS/XIV/SVC Configuration Guide
---
IBM DS/XIV/SVC are using embedded SMI-S provider which
ran by the storage array itself. Please contact IBM
support to enable SMI-S feature.

Once done, the URI is:

        smispy://username@host:5988/?namespace=root/ibm
        smispy+ssl://username@host:5989/?namespace=root/ibm
        # The 'username' is the username of management account of IBM
        # array.
        # The 'host' is the ip/host of management port of IBM array.

For 'SSL error: certificate verify failed' error, it means LSM failed to
verify IBM SMI-S providers' SSL public keys with system wide PKI/x509.
Please contact IBM support to setup the self-signed certification.

If you want to ignore the SSL check error which is not suggested, please
add 'no_ssl_verify=yes' in your URI, like this:

        smispy+ssl://username@host:5988/?namespace=root/ibm&no_ssl_verify=yes
