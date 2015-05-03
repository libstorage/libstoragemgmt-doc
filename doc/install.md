---
title: Installation Guide
---

* [1. Install] (#1.-install)
  * [1.1 RPM Package of Released Version]
    (#1.1.-rpm-package-of-released-version)
  * [1.2 Compile from Source] (#1.2.-compile-from-source)
  * [1.3 Weekly Snapshot Build] (#1.3.-weekly-snapshot-build)
* [2. Start daemon] (#2.-start-daemon)
* [3. Test installation] (#3.-test-installation)

## 1. Install

There are a few different ways to install libStorageMgmt.
Select the method that works best for your setup.

### 1.1. RPM Package of Released Version
For RHEL/Centos 6 (in EPEL), RHEL/Centos 7 and Fedora you can install
LSM via these commands:

```bash
$ sudo yum search libstoragemgmt
$ sudo yum install libstoragemgmt
# Install plugins.
$ sudo yum install libstoragemgmt-\*-plugin
$ sudo systemctl start libstoragemgmt.service
```

The library is packaged into separated RPMs:

* `libstoragemgmt` -- Daemon, CLI util and basic files.

* `libstoragemgmt-python` -- Python client libraries.

* `libstoragemgmt-*-plugin` -- Plugins.

* `libstoragemgmt-devel` -- Development files for C language.

* `libstoragemgmt-udev` -- Udev files for auto-rescan on storage
  configuration changes(SCSI Unit Attention ASC/ASCQ).

For libstoragemgmt-smis-plugin on RHEL7, the optional repo
is required for pywbem package:

```bash
# RHEL 7 Server
$ sudo subscription-manager repos --enable \
    rhel-7-server-optional-rpms
# RHEL 7 Workstation
$ sudo subscription-manager repos --enable \
    rhel-7-workstation-optional-rpms
```

For OpenSuSE 13.1/13.2, the packages have been renamed to:

* `libstoragemgmt1` -- Daemon, CLI util and basic files.

* `libstoragemgmt1-python` -- Python client libraries.

* `libstoragemgmt1-*-plugin` -- Plugins.

* `libstoragemgmt-devel` -- Development files for C language.

### 1.2. Compile from Source

1. Download source or clone from git repo.

    ```bash
    # Source tarball from
    #
    # https://github.com/libstorage/libstoragemgmt/releases/latest
    #
    # Or Use git:
    $ git clone https://github.com/libstorage/libstoragemgmt.git
    ```

2. Install required packages.

    RHEL/Centos and Fedora

    ```bash
    $ sudo yum install \
        tar make gcc gcc-c++ libtool autoconf automake \
        yajl-devel pywbem libxml2-devel  check-devel glib2-devel \
        m2crypto openssl-devel libconfig-devel

    # For RHEL/Centos 6 with EPEL
    $ sudo yum install python-argparse -y

    # If you want the REST API daemon
    $ sudo yum install libmicrohttpd-devel json-c-devel
    ```

    SuSE and OpenSuSE

    ```bash
    $ sudo zypper in gcc tar make gcc gcc-c++ libtool autoconf \
        automake libyajl-devel python-pywbem libxml2-devel check-devel \
        glib2-devel python-M2Crypto openssl-devel libcofig-devel

    # If you want the REST API daemon
    $ sudo zypper in libjson-devel procps libmicrohttpd-devel
    ```

    Debian

    ```bash
    $ sudo apt-get install gcc tar make g++ libtool autoconf automake \
        libyajl-dev python-pywbem libxml2-dev check \
        libglib2.0-dev python-m2crypto libssl-dev libconfig-dev

    # If you want the REST API daemon
    $ sudo apt-get install libmicrohttpd-dev libjson-c-dev procps
    ```

4. Compile and install:

    ```bash
    # autogen.sh required when using git source tree
    $ ./autogen.sh
    $ ./configure
    # Options:
    #       --without-rest-api      # skip REST API daemon, experimental
    #       --without-megaraid      # skip megaraid plugin.

    $ make -j5
    $ sudo make install
    ```

### 1.3. Weekly Snapshot Build

* Download the repo file from [OBS][1] for your system to folder
  '/etc/yum.repos.d/':

* Install libstoragemgmt(RHEL/Centos and Fedora):

    ```bash
    $ yum search libstoragemgmt
    $ sudo yum install libstoragemgmt-<desired package>
    ```

* Install libstoragemgmt(OpenSuSE):

    ```bash
    $ zypper se libstoragemgmt1
    $ sudo zypper in libstoragemgmt1-<desired package>
    ```


## 2. Start daemon

```bash
# System without systemd, like RHEL 6
$ sudo service libstoragemgmtd start

# Systemd
$ sudo systemctl start libstoragemgmt.service
```

## 3. Test installation

```bash
$ lsmcli -u 'sim://' list --type systems
```

[1]: http://download.opensuse.org/repositories/home:/cathay4t:/libstoragemgmt-git/
