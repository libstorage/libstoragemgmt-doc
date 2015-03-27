---
title: General SMI-S provider Configuration Guide
---

Please contact storage vendor support for enabling SMI-S service.
Once done, the URI is:

        smispy://username@host:5988/?namespace=root/vendor
        smispy+ssl://username@host:5989/?namespace=root/vendor
        # The 'username' is the username of management account of
        # SMI-S service.
        # The 'host' is the ip/host of management port of SMI-S
        # service.
        # The '5988' and '5989' is the default port number of SMI-S
        # service, change it if your SMI-S service use other ports.

For 'SSL error: certificate verify failed' error, it means LSM failed to
verify your SMI-S providers' SSL public keys with system wide PKI/x509.
Please contact SMI-S vendor's support to setup the self-signed certification.

If you want to ignore the SSL check error which is not suggested, please
add 'no_ssl_verify=yes' in your URI, like this:

        smispy+ssl://username@host:5988/?namespace=root/vendor&no_ssl_verify=yes
