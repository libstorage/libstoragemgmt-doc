---
title: Library Developer Guide
---

This document is assuming you already read the [user_guide][1].

## 1. Setup developer environment

### 1.1 Grab the code

```bash
# The rest document is assuming your working folder is $HOME
# which store the git repo as "$HOME/libstoragemgmt" folder.
$ git clone https://github.com/libstorage/libstoragemgmt.git
```

### 1.2 Install build dependencies

#### 1.2.1 RHEL/Centos/Fedora

```bash
# Make sure build dependencies are installed
# For EL6 install python-argparse also
$ sudo yum install tar make gcc gcc-c++ libtool autoconf automake \
       yajl-devel pywbem libxml2-devel  check-devel glib2-devel \
       m2crypto libmicrohttpd-devel json-c-devel \
       openssl-devel perl-Config-IniFiles time PyYAML libconfig-devel
```

#### 1.2.2 OpenSuSE

```bash
$ sudo zypper in gcc tar make gcc gcc-c++ libtool autoconf automake \
    libyajl-devel python-pywbem libxml2-devel check-devel \
    glib2-devel python-M2Crypto openssl-devel python-PyYAML \
    libjson-devel procps libmicrohttpd-devel libconfig-devel
```

## 2. Compile

```bash
# Change into root of source tree
$ cd libstoragemgmt

# Setup autotools
$ ./autogen.sh

# Configure
$ ./configure

# Build it
$ make
```

## 3. Run from the code tree

### 3.1 Automatic way -- lsmenv

```bash
# EPEL7 is needed for perl-Config-IniFiles on RHEL/Centos 7
$ sudo yum install perl-Config-IniFiles
# or
# sudo zypper in perl-Config-IniFiles

# Link 'lsmenv' to $HOME/bin
# Assuming libstoragemgmt is in "$HOME" and $HOME/bin is in $PATH
# Check 'lsmenv -h' for detail.
$ ln -s $HOME/libstoragemgmt/tools/lsmenv $HOME/bin/

# Start lsmd daemon from code tree
$ lsmenv lsmd

# Invoke lsmcli command
$ lsmenv sim lsmcli list --type pools

# Invoke plugin test
$ lsmenv sim plugin_test
```


### 3.2 Manual way

```bash
# Assuming libstoragemgmt is in "$HOME"

# Create socket folder:
$ mkdir /tmp/lsm/ipc/

# Link lsmcli and plugin into lsm python binding folder
$ ln -sv ../../plugin $HOME/libstoragemgmt/python_binding/lsm/

# Export LSM required environment variables
$ export LSM_UDS_PATH="/tmp/lsm/ipc/"
$ export PYTHONPATH=\
    "$PYTHONPATH:$HOME/libstoragemgmt/python_binding/lsm"
$ export LD_LIBRARY_PATH=\
    "$LD_LIBRARY_PATH:$HOME/libstoragemgmt/c_binding"

# Run lsmd daemon
$ $HOME/libstoragemgmt/daemon/lsmd

# Run lsmcli
$ $HOME/libstoragemgmt/tools/lsmcli/lsmcli ls -u sim://

# Invoke plugin test
$ $HOME/libstoragemgmt/test/plugin_test.py
```

## 2. Code Workflow

```text
            User application(for example: lsmcli)
                        ^
                        |
                        |
                        v
     +------------------+-----------------------+
     |                                          |
   Python API           or                    C API
     |                                          |
     +------------------------------------------+
            |                           ^
            | Initial call              |
            v                           |
    Socket of LSM Daemon                |
            |                           |
            | Invoke and setup          |
            | plugin                    |
            |                           v
            +-------------------> Socket of LSM plugin
                                        ^
                                        |
                                        |
                                        | Storage Vendor SDK
                                        |
                                        v
                                 Storage Array/RAID
```

The communication between client and LSM daemon/plugin is based on unix
domain socket (default folder is /var/run/lsm/ipc/) and using JSON-RPC
for protocol.

Example:

![lsm_pool_work_flow](../images/storage_pools.png)


## 3. C Library Code Layout

The C library code is located at 'libstoragemgmt/c_binding' folder.
It basically uses C++ codes internally for data converting between
socket JSON to LSM C API.

TODO: Explain which lib we are using for JSON, socket, and etc.


### 3.1 c_binding/include/libstoragemgmt

Only 'libstoragemgmt.h' is for client use. (right?).

### 3.2 c_binding/lsm_datatypes

Explain here what these files (cpp and hpp) for.

### 3.3 c_binding/lsm_convert

### 3.4 add more files

## 4. Python Library Code Layout

The Python libraray code is located at 'libstoragemgmt/python_binding'
folder.

### 4.1 _client.py

This is the sample code of client:

```
#!/usr/bin/python2
import lsm

lsm_cli_obj = lsm.Client("sim://")      # Make connection.

pools = lsm_cli_obj.pools()             # Enumerate Storage Pools.
```

This file is providing the lsm.Client class and its methods.

Some common check also implement here.

### 4.2 _common.py

This file is providing lsm.ErrorNumber, lsm.LsmError for public client
use.

The Proxy class is for internal use, it make sure all other python
exception is wraped into LsmError.

The 'ErrorLevel' class is not used anywhere. Might be removed in the
future.

### 4.3 _data.py

This file is providing LSM classes defination like lsm.System, lsm.Pool
and etc.

### 4.4 _iplugin.py

Provide a wrapper class `INetworkAttachedStorage`,
`IStorageAreaNetwork`, and `INfs` to raise
LsmError.ErrorNumber.NO_SUPPORT when plugin does not implement user
requested methods.

### 3.5 _pluginrunner.py

Provide class `PluginRunner` to server in `xxx_lsmplugin`.
Please check `libstoragemgmt/plugin/sim/sim_lsmplugin` for detail.

### 3.6 _transport.py

Used by `_client.py` to provide communication between plugin and user
application.

### 3.7 version.py

Generated by autoconf tools for `VERSION` constant.

## 4. LSM Daemon Code layout.

The daemon code is located at "libstoragemgmt/daemon" folder.

### 4.1 lsmd -- `lsm_daemon.c`

Code workflow:

1. Scan `--plugindir` argument defined folder excursively for plugins.
   All executable file named with `_lsmplugin` suffix will be consider
   as a lsm plugin.

2. Create a UNIX STREAM socket in `--socketdir` folder for each plugin.
   Example, for `sim_lsmplugin` plugin, and `/tmp/lsm/ipc` as socketdir,
   a UNIX STREAM socket will be created at `/tmp/lsm/ipc/sim`.

3. Once received data on any UNIX socket, invoke the plugin binary with
   the socket filer description number as first argument.

4. The plugin binary will reply API request via UNIX socket.

### 4.2 `lsm_restd`
Still in experimental stage.
Using `microhttpd.h` for http server, `json/json.h` for JSON parsing,
`libxml/uri.h` for URI parsing.

As LSM internally use JSON data for pluing-client communication, this daemon
simply convert JSON request from web service to LSM JSON format.

## 5. Tests

The 'make check' command will run three tests via `runtest.sh`.

### 5.1 C Unit Test -- `tester.c`
Designed to test C API against simc and sim plugin.

### 5.2 lsmcli Test -- `cmdtest.py`

Designed to test lsmcli against sim plugin.

### 5.3 Plugin Test(Python API) -- `plugin_test.py`

Designed to test all plugin via Python API.

## 6. How to contribute.

1. Discuss in maillist if it's a new feature or big changes.
2. Work on patches
3. git format patch
4. Make sure your patch meet these requirements:
    * Python PEP8(python-pep8) pass.
    * Python code static check(pylint): no error or fatal.
    * "make distcheck" pass.
    * "make rpm" pass.
    * No regression for plugin_test against changed plugin.
    * Every patch should leave the code tree in a working state
    * WS & spelling corrections in separate patch.
    * Every patch only contain one small change.
    * Patch set only contain one serial change.
    * Test code should be included if changed area is not tested.
5. git send-email

[1]: user_guide.html
