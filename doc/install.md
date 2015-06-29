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

For OpenSuSE 13.1/13.2/Tumbleweed, the packages have been renamed to:

* `libstoragemgmt` -- Daemon, CLI util and basic files.

* `libstoragemgmt1` -- C library files.

* `python-libstoragemgmt` -- Python client libraries.

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

    Debian/Ubuntu

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
    $ ./configure --prefix=/usr
    # If you define other prefix, '--plugindir <you_prefix>' of lsmd
    # should be use.
    # Extra options:
    #       --without-rest-api      # skip REST API daemon, experimental
    #       --without-megaraid      # skip megaraid plugin.
    #       --without-hpsa          # skip hpsa plugin.

    $ make -j5
    $ sudo make install
    ```

### 1.3. Weekly Snapshot Build

* Install weekly snapshot rpm repo:

    * [Fedora][1]

    * [RHEL/Centos 6][2]

    * [RHEL/Centos 7][3]

    * [OpenSuSE][4]

* Install libstoragemgmt(RHEL/Centos and Fedora):

    ```bash
    $ yum search libstoragemgmt
    $ sudo yum install libstoragemgmt-<desired package>
    ```

* Install libstoragemgmt(OpenSuSE):

    ```bash
    $ zypper se libstoragemgmt
    $ sudo zypper in libstoragemgmt-<desired package>
    ```

## 2. Prepare and Start daemon

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
$ sudo /usr/bin/lsmd -d -v

# OS with Systemd
$ sudo systemctl start libstoragemgmt.service
```

## 3. Test installation

```bash
$ lsmcli -u 'sim://' list --type systems
```

[1]: http://download.opensuse.org/repositories/home:/cathay4t:/libstoragemgmt-git-fedora/
[2]: http://download.opensuse.org/repositories/home:/cathay4t:/libstoragemgmt-git-rhel6/CentOS_6/home:cathay4t:libstoragemgmt-git-rhel6.repo
[3]: http://download.opensuse.org/repositories/home:/cathay4t:/libstoragemgmt-git-rhel7/CentOS_7/home:cathay4t:libstoragemgmt-git-rhel7.repo
[4]: http://download.opensuse.org/repositories/home:/cathay4t:/libstoragemgmt-git-suse/
