---
title: Installation Guide
---

* TOC
{:toc}

## 1. Install

There are a few different ways to install libStorageMgmt.
Select the method that works best for your setup.

### 1.1. RPM Package of Released Version
For RHEL/Centos 6 (in EPEL), RHEL/Centos 7 and Fedora you can install
LSM via these commands:

```bash
sudo yum install libstoragemgmt
# Install plugins.
sudo yum install libstoragemgmt-\*-plugin
sudo systemctl start libstoragemgmt.service
```

The library is packaged into separated RPMs:

* `libstoragemgmt` -- Daemon, CLI util and basic files.

* `libstoragemgmt-python` -- Python client libraries.

* `libstoragemgmt-python-clibs` -- Python client C extension.

* `libstoragemgmt-*-plugin` -- Plugins.

* `libstoragemgmt-devel` -- Development files for C language.

* `libstoragemgmt-udev` -- Udev files for auto-rescan on storage
  configuration changes(SCSI Unit Attention ASC/ASCQ).

For libstoragemgmt-smis-plugin on RHEL7, the optional repo
is required for pywbem package:

```bash
# RHEL 7 Server
sudo subscription-manager repos --enable \
    rhel-7-server-optional-rpms
# RHEL 7 Workstation
sudo subscription-manager repos --enable \
    rhel-7-workstation-optional-rpms
```

For OpenSuSE 13.1/13.2/Tumbleweed, the packages have been renamed to:

* `libstoragemgmt` -- Daemon, CLI util and basic files.

* `libstoragemgmt1` -- C library files.

* `python-libstoragemgmt` -- Python client libraries.

* `python-libstoragemgmt-clibs` -- Python client libraries C extension.

* `libstoragemgmt-*-plugin` -- Plugins.

* `libstoragemgmt-devel` -- Development files for C language.

### 1.2. Compile from Source

1. Download source or clone from git repo.

```bash
# Source tarball from
#
# https://github.com/libstorage/libstoragemgmt/releases/latest
#
# Or Use git:
git clone https://github.com/libstorage/libstoragemgmt.git
```

2. Install required packages.

```bash
# RHEL/Centos and Fedora <= 28
sudo [yum|dnf] install `cat rh_py2_rpm_dependency`

Fedora >= 29
sudo dnf install `cat rh_py3_rpm_dependency`

# SuSE and OpenSuSE
sudo zypper in `cat suse_rpm_dependency`

# Debian/Ubuntu

sudo apt-get install `cat deb_dependency`
```

3. Compile and install:

```bash
# autogen.sh required when using git source tree
./autogen.sh
./configure --prefix=/usr

# Fedora >= 29 will require '--with-python3'

# If you define other prefix, '--plugindir <you_prefix>' of lsmd
# should be use.
# Extra options:
#       --without-megaraid      # skip megaraid plugin.
#       --without-hpsa          # skip hpsa plugin.

make -j5
sudo make install
```

## 2. Prepare and Start daemon

__ONLY REQUIRED IF YOU ARE BUILD FROM SOURCE__

```bash
# Create libstoragemgmt user and group
sudo groupadd -r libstoragemgmt
sudo useradd -r -g libstoragemgmt -d /var/run/lsm -s /sbin/nologin \
    -c "daemon account for libstoragemgmt" libstoragemgmt

# Create IPC socket folders
sudo mkdir -p /var/run/lsm/ipc
sudo chmod 0755 /var/run/lsm
sudo chmod 0755 /var/run/lsm/ipc
sudo chown libstoragemgmt:libstoragemgmt /var/run/lsm
sudo chown libstoragemgmt:libstoragemgmt /var/run/lsm/ipc

# OS without systemd, like RHEL 6, Debian 7,
# or Ubuntu 12.04/14.04 LTS
sudo /usr/bin/lsmd -d -v

# OS with Systemd
sudo systemctl start libstoragemgmt.service
```

## 3. Test installation

```bash
lsmcli -u 'sim://' list --type systems
```
