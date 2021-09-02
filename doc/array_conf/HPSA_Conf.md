---
title: HP SmartArray Configuration Guide
---
LibStorageMgmt can manage HP SmartArray RAID adapters via the hpsa plugin
(`libstoragemgmt-hpsa-plugin` package).
You are also required to install the `ssacli` binary tool from the HP
website.

Once done, the URI will be:

```
hpsa://
```

Note: Older versions of command line tool were `hpssacli`, newer versions are
`ssacli`.  They have the same syntax, the plugin and URI still use old naming.

If you are not using default install path of `ssacli`, please
use this URI to specify the path:

```
hpsa://?hpssacli=<path_of_hpssacli_bin_file>
```

<p style="color: red">
<b>
The following are the steps used by ourselves to install ssacli
<br>
We are providing this information in hopes of saving you time with NO WARRANTY.
<br>
Please contact HP Support if you encounter issues with the command line tool.
</b>
</p>

* Create `/etc/yum.repos.d/ssacli.repo` with this content:

```
[hp-mcp]
name=HP Management Component Pack
baseurl=http://downloads.linux.hpe.com/SDR/repo/spp/RedHat/$releasever/$basearch/current/
enabled=1
gpgcheck=0
```
* Execute this commands to install hpssacli rpms:

```
sudo yum install ssacli -y
```

Above steps were tested on:

* `Fedora 34`
* `ssacli-4.21-7.0.x86_64.rpm` and `ssacli-5.10-44.0.x86_64.rpm`
