---
title: Nexenta Storage Configuration Guide
---
The libStorageMgmt can manage NexentaStor 4.x/3.x via Nstor plugin
(`libstoragemgmt-nstor-plugin` package).

NexentaStor REST service should be enabled. Please contact Nexenta
support for detail.

Once done, the URI will be:

```
nstor://username@host
nstor+ssl://username@host
# The 'username' is the account with 'Can_use_restapi' permission.
# The 'host' is ip/hostname of nextenta management interface.
```

<p style="color: red">
<b>
The following are the steps used by ourselves for Nexenta
<br>
installation and configuration.
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact Your Linux Support instead if are working on production
system.
</b>
</p>

* Once Nexenta installed. Open this link:

    ```
    # NexentaStor 3.x
    http://<ip_of_nstor>:2000/settings/users/
    # NexentaStor 4.x
    http://<ip_of_nstor>:8457/settings/users/
    ```

* Choose a user for LSM and enable 'Can_use_restapi' right.

Above steps were tested on:

* Nexenta Community Edition 3.1.5 in VirutalBox.
