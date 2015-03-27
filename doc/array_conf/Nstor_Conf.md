---
title: Nexenta Storage Configuration Guide
---
Currently, we only support Nexenta 3.x with http or https
web management interface enabled. We are using Nexenta REST API
for management.

You need to install the package libstoragemgmt-nstor-plugin.noarch if you
didn't install using the distribution tarball.

Once done, the URI will be:

        nstor://username@host
        nstor+ssl://username@host
        # The 'username' is the account with 'Can_use_restapi'
        # permission.
        # The 'host' is ip/hostname of nextenta management
        # interface.

<p style="color: red">
<b>
The following are the steps used by ourselves for Nexenta
<br>
installation and configuration.
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact Your Linux Support instead if are working on production system.
</b>
</p>

* Once Nexenta installed. Open this link:

        http://nstor3:2000/settings/users/

* Choose a user for LSM and enable 'Can_use_restapi' right.


Above steps were tested on:

* Nexenta Community Edition 3.1.5 in VirutalBox.
