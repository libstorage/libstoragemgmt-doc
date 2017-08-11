---
title: Microsemi SAS Controllers Configuration Guide
---
LibStorageMgmt can manage Arcconf SmartArray via arcconf plugin
(`libstoragemgmt-arcconf-plugin` package).
You are also required to install `arcconf` binary tool from Microsemi
website. The arcconf tool is also included in Microsemi maxView Storage
Manager tool

Once done, the URI will be:

```
arcconf://
```

If you are not using default install patch of `arcconf`, please
use this URI to specify the path:

```
arcconf://?arcconf=<path_of_arcconf_bin_file>
```

<p style="color: red">
<b>
The following are the steps used by ourselves to get arcconf
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact Microsemi Support instead if you are working on production
system.
</b>
</p>

* Execute this commands to install arcconf executables:

```
./StorMan-2.05-22885.x86_64.bin
```

Above steps were tested on:

* `RHEL 7.3 x86_64`
* `StorMan-2.05-22885.x86_64.bin`
