---
title: Linux Targetd Configuration Guide
---
Linux targetd is used to control the Linux LVM volume and LIO iscsi
target.  Please follow documents in https://github.com/agrover/targetd
to install and setup on the system that will be providing the storage.
If you are using Fedora 19+ or RHEL7+ rpm packages of targetd are
included in the standard repositories.

For libStorageMgmt you will need to install the
`libstoragemgmt-targetd-plugin.noarch` package if you didn't install
using the distribution tarball.

Once done, the URI will be:

```
targetd://username@host
targetd+ssl://username@host
# The 'username' is the account configured in
# /etc/target/targetd.yaml
# The 'host' is ip/hostname where targetd installed.
```

<p style="color: red">
<b>
The following are the steps used by ourselves for targetd
<br>
installation and configuration.
<br>
We are providing this in the favor of saving your time with NO WARRANTY.
<br>
Please contact Your Linux Support instead if are working on production
system.
</b>
</p>

* Execute these commands to install targetd:

```bash
sudo yum install targetd -y
```

* Changed `/etc/target/targetd.yaml` like below:

```yaml
user: admin
password: redhat
block_pools: [vg_targetd, vg_targetd_thin/ThinPoolLV]
ssl: false
target_name: iqn.2003-01.com.redhat.gris:iscsi-tgt
fs_pools: [/home/targetd]
# The fs_pools should contain btrfs mount point.
```

* Start targetd daemon and enable on-boot:

```bash
systemctl enable targetd
systemctl start targetd
```

Above steps were tested on:

* `RHEL 7.1 x86_64`
* `targetd-0.8.2-1.el7.noarch`
