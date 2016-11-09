---
title: HP SmartArray Configuration Guide
---
LibStorageMgmt can manage HP SmartArray via hpsa plugin
(`libstoragemgmt-hpsa-plugin` package).
You are also required to install `hpssacli` binary tool from HP
website.

Once done, the URI will be:

```
hpsa://
```

If you are not using default install patch of `hpssacli`, please
use this URI to specify the path:

```
hpsa://?hpssacli=<path_of_hpssacli_bin_file>
```

<p style="color: red">
<b>
The following are the steps used by ourselves to get hpssacli
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact HP Support instead if are working on production system.
</b>
</p>

* Create `/etc/yum.repos.d/HP_hpssacli.repo` with this content:

```
[hp-mcp]
name=HP Management Component Pack
baseurl=http://downloads.linux.hpe.com/SDR/repo/spp/RedHat/$releasever/$basearch/current/
enabled=1
gpgcheck=0
```
* Execute this commands to install hpssacli rpms:

```
sudo yum install hpssacli -y
```

Above steps were tested on:

* `RHEL 7.1 x86_64`
* `hpssacli-2.20-11.0.x86_64.rpm`
