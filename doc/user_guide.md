---
title: User Guide
---

* [1. Installation](#1.-installation)
* [2. Warnings](#2.-warnings)
* [3. Terminology](#3.-terminology)
* [4. How to use](#4.-how-to-use)
 * [4.1. Command Line Tool Example](#4.1.-command-line-tool-example)
 * [4.2. Python Code Example](#4.2.-python-code-example)
 * [4.3. C code Example](#4.3.-c-code-example)
* [5. Assistance or feedback](#5.-assistance-or-feedback)
* [6. Contribute](#6.-contribute)

LibStorageMgmt is a vendor neutral library that provides an API and
tools for managing SAN arrays and local hardware RAID adapters.
This is a community open source project (LGPL 2.1+ license), providing,
but not limited to, the following features:

 * Storage system, pool, volume, filesystem, disk monitoring.
 * Volume create, delete, resize, and mask.
 * Volume snapshot, replication create and delete.
 * NFS file system create, delete, resize and expose.
 * Access group create, edit, and delete.

Server resources such as CPU and interconnect bandwidth are not utilized
because the operations are all done on the array.

The package provides:

 * Stable C and Python API for client application and plug-in developers.
 * Command line interface that utilizes the library (lsmcli).
 * Daemon that executes the plug-in in a separate process (lsmd).
 * Simulator plug-in that allows the testing of client applications (sim).
 * Plugin architecture for interfacing with arrays.

The libStorageMgmt uses a URI to identify which plugin should be used.
The URI format is:

```text
plugin://<username>@host:<port>/?<query string parameters>
plugin+ssl://<username>@host:<port>/?<query string parameters>
# All plugins except 'simc://' and 'sim://' support ssl encryption.
```

Current plugins and supported storage products:

  Plugin  |  URI Syntax  | Support Products
--------- | ------------ | ----------------
Simulator C |`simc://` | Only for development or testing client applications
Simulator | `sim://` | Only for development or testing client applications
ONTAP  | `ontap://<user>@<host>`  | [NetApp ONTAP][1]
SMI-S  | `smispy://<user>@<host>` | [EMC VMAX/DMX/VNX/CX][2]
SMI-S  | `smispy://<user>@<host>` | [NetApp ONTAP][1]
SMI-S  | `smispy://<user>@<host>` | [IBM XIV/DS/SVC][3]
SMI-S  | `smispy://<user>@<host>?namespace=root/lsiarray13` | [NetApp E-Series][5]
SMI-S  | `smispy://<user>@<host>` | [Other Array with SMI-S 1.4+][6]
Targetd | `targetd://<user>@<host>` | [Linux Targetd][7]
Nstor | `nstor://<user>@<host>` | [NexentaStor 3.x][8]
MegaRAID | `megaraid://` | [LSI MegaRAID][4]

## 1. Installation
The libStorageMgmt packages exist in RHEL 7 and Fedora repositories.
EL6 support is available in fedora EPEL repository.

To install libStorageMgmt for use of the command line, required run-time
libraries and simulator plug-ins use the following command:

```bash
$ sudo yum install libstoragemgmt
```

To develop C applications that utilize the library, install the
libstoragemgmt-devel and, optionally the libstorage-debuginfo packages
with the following command:

```bash
$ sudo yum install libstoragemgmt-devel libstoragemgmt-debuginfo
```

To install libStorageMgmt for use with hardware arrays, select one or
more of the appropriate plug-in packages with the following command:

```bash
$ sudo yum install libstoragemgmt-smis-plugin \
    libstoragemgmt-netapp-plugin \
    libstoragemgmt-nstor-plugin \
    libstoragemgmt-targetd-plugin \
    libstoragemgmt-megaraid-plugin
```

Please refer to [install_guide][15] for detailed information on
installation.

## 2. Warnings

This library and associated tools have the ability to __destroy any and
all data__ located on arrays that it manages. It is highly recommended
to develop and test applications and scripts against the storage
simulator plug-in to remove any logic errors before working with
production systems. Testing applications and scripts on actual
non-production hardware before deploying to production is strongly
encouraged if possible.

## 3. Terminology

<table border="3">

<tr>
<th>Term </th>
<th>Meaning </th>
<th>Synonym </th>
</tr>

<tr>
<td>System</td>
<td>
    Represents a Storage Array or direct attached storage RAID. Examples
    include:
<ul>
    <li> A hardware RAID card, LSI MegaRAID
    <li> A storage area network (SAN), EMC VNX, NetApp Filer
    <li> A software solution running on commodity hardware, targetd, Nexenta
</ul>

</td>
<td>None</td>
</tr>

<tr>
<td>Pool</td>
<td>
    A group of storage space, typically File Systems or Volumes can be created
    from a Pool.
</td>
<td>StoragePool(SNIA Terminology)</td>
</tr>

<tr>
<td>Volume</td>
<td>
    Storage Area Network (SAN) Storage Arrays can expose a Volume to the
    Host Bus Adapter (HBA) over different transports (FC/iSCSI/FCoE/etc.)
    The host OS treats it as block devices(one volume can be exposed as many
    disks if multipath[2] is enabled).
</td>
<td>LUN(Logical Unit Number), StorageVolume(SNIA Terminology), Virtual
    disk
</td>
</tr>

<tr>
<td>Filesystem</td>
<td>
    Network Attached Storage (NAS) Storage array can expose a Filesystem to
    host OS via IP network using NFS or CIFS protocol. The host OS treats it as
    a mountpoint or a folder containing files depending on client operating
    system.
</td>
<td>None</td>
</tr>

<tr>
<td>Disk</td>
<td>
    Physical disk holding the data.  Pools typically consist of one or more
    disks.
</td>
<td>DiskDrive(SNIA Terminology)</td>
</tr>

<tr>
<td>Initiator</td>
<td>
    In fibre channel (FC) or fibre channel over ethernet (FCoE), Initiator is
    WWPN(World Wide Port Name)[3] or/and WWNN(World Wide Node Name).
    In iSCSI, Initiator is the IQN(iSCSI Qualified Name)[4].
    In NFS or CIFS, Initiator is the host name or IP address of host.
</td>
<td>None</td>
</tr>

<tr>
<td>Access group</td>
<td>
    Collections of iSCSI/FC/FCoE initiators which are granted access to one or
    more Storage volumes. This ensures that only storage volumes are
    accessible by the specified initiator(s).
</td>
<td>Initiator group(igroup), Host Group</td>
</tr>

<tr>
<td>Volume Mask</td>
<td>
    Exposing a Volume to a specified Access Group. The libStorageMgmt
    library currently does not support logical unit masking with the ability to
    choose a specific logical unit number (LUN). The libStoragemgmt library
    lets the storage array select the next available LUN for assignment. Make
    sure you read the OS, Storage Array, or HBA documents if you are
    configuring boot from SAN or masking 256+ Volumes.  Volumes are masked to
    all target ports. Futures versions of libStorageMgmt may allow you to
    specify specific target port.
</td>
<td>LUN Mapping, LUN masking</td>
</tr>

<tr>
<td>Volume Unmask</td>
<td>
    Reverse of volume mask
</td>
<td>LUN unmap, LUN unmask</td>
</tr>

<tr>
<td>Clone</td>
<td>Point in time read writeable space efficient copy of data </td>
<td>Read writeable snapshot</td>
</tr>

<tr>
<td>Copy</td>
<td>Full bitwise copy of the data (occupies full space) </td>
<td>None</td>
</tr>

<tr>
<td>Mirror SYNC</td>
<td>
    I/O will be blocked until I/O reached both source and target storage
    systems. There will be no data difference between source and target
    storage systems.
</td>
<td>None</td>
</tr>

<tr>
<td>Mirror ASYNC</td>
<td>
    I/O will be blocked until I/O reached source storage systems.
    The source storage system will use copy the changes data to target
    system in a predefined interval.
    There will be a small data differences between source and target.
</td>
<td>None</td>
</tr>

<tr>
<td>Snapshot</td>
<td>
    A point in time (PIT), read only, space efficient copy of a file
    system.
</td>
<td>Read only snapshot</td>
</tr>

<tr>
<td>Child dependency</td>
<td>
    Some arrays have an implicit relationship between the origin (parent
    Volume or File system) and the child (eg. Snapshot, Clone). For example
    you cannot delete the parent if it has one or more dependent children.
    The API provides methods to determine if any such relationship exists and
    a method to remove the dependency by replicating the required blocks.
</td>
<td>None</td>
</tr>


</table>

## 4. How to use

These are mandatory needs:

* Required libStorageMgmt plugin installed.

* libStorageMgmt daemon -- lsmd started.

    Normally, "systemctl start libstoragemgmt.service" will suffice.

* Provide correct URI and password.

### 4.1. Command Line Tool Example
Please check manpage of `lsmcli` for details.

```bash
$ sudo systemctl start libstoragemgmt.service
$ export LSMCLI_URI='sim://'
$ unset LSMCLI_PASSWORD
$ lsmcli list --type volumes
ID              | Name       | SCSI VPD 0x83                    | Size         ...
------------------------------------------------------------------------------ ...
VOL_ID_00000001 | Volume 000 | 54c71dff7388205e8694a837f12c290c | 214748364800 ...
VOL_ID_00000002 | Volume 001 | 88e35c747a4c3a92df2070b81e841c62 | 214748364800 ...
```

### 4.2. Python Code Example
Please refer to [libStorageMgmt Python API guide][9] for detail.

```python
#!/usr/bin/python2
import lsm

# Make connection.
lsm_cli_obj = lsm.Client("sim://")

# Enumerate Storage Pools.
pools = lsm_cli_obj.pools()

# Use pool information.
for p in pools:
    print 'pool name:', p.name, 'freespace:', p.free_space

# Close connection
if lsm_cli_obj is not None:
    lsm_cli_obj.close()
    print 'We closed'
```

### 4.3. C code Example
Please refer to [LibStorageMgmt C API document][10] for detail.

```c
#include <stdio.h>
#include <libstoragemgmt/libstoragemgmt.h>

/*
 * If you have the development library package installed

   $ gcc -Wall client_example.c -lstoragemgmt -o client_example

 *
 * If building out of source tree
 *
   $ gcc -Wall -g -O0 client_example.c -I../c_binding/include/ \
           -L../c_binding/.libs -lstoragemgmt -o client_example

 */

void error(char *msg, int rc, lsm_error *e)
{
    if( rc ) {
        printf("%s: error: %d\n", msg, rc);

        if( e && lsm_error_message_get(e) ) {
            printf("Msg: %s\n", lsm_error_message_get(e));
            lsm_error_free(e);
        }
    }
}

void list_pools(lsm_connect *c)
{
    lsm_pool **pools = NULL;
    int rc = 0;
    uint32_t count = 0;

    rc = lsm_pool_list(c, NULL, NULL, &pools, &count, LSM_FLAG_RSVD);
    if( LSM_ERR_OK == rc ) {
        uint32_t i;
        for( i = 0; i < count; ++i) {
            printf("pool name: %s freespace: %"PRIu64"\n",
                lsm_pool_name_get(pools[i]),
                lsm_pool_free_space_get(pools[i]));
        }

        lsm_pool_record_array_free(pools, count);
    } else {
        error("Volume list", rc, lsm_error_last_get(c));
    }
}

int main()
{
    lsm_connect *c = NULL;
    lsm_error *e = NULL;
    int rc = 0;

    const char *uri = "sim://";

    rc = lsm_connect_password(uri, NULL, &c, 30000, &e, LSM_FLAG_RSVD);

    if( LSM_ERR_OK == rc ) {
        printf("We connected...\n");

        list_pools(c);

        rc = lsm_connect_close(c, LSM_FLAG_RSVD);
        if( LSM_ERR_OK != rc ) {
            error("Close", rc, lsm_error_last_get(c));
        } else {
            printf("We closed\n");
        }
    } else {
        error("Connect", rc, e);
    }

    return rc;
}
```

## 5. Assistance or feedback

We'd love to hear from you.

For general questions please contact us via email or IRC:

* An email to <libstoragemgmt-users@lists.fedorahosted.org>
* Ping "Gris" or "tasleson" in #libStorageMgmt channel of [FreeNode IRC][11]

Bugs reports or suspected bug reports can be emailed to:

* <libstoragemgmt-devel@lists.fedorahosted.org>

User mailing list archive page:
https://lists.fedorahosted.org/mailman/listinfo/libstoragemgmt-users

Developer mailing list archive page:
https://lists.fedorahosted.org/mailman/listinfo/libstoragemgmt-devel

Web IRC page:
https://webchat.freenode.net

## 6. Contribute

Please subscribe to "libstoragemgmt-devel@lists.fedorahosted.org" mailist:
https://lists.fedorahosted.org/mailman/listinfo/libstoragemgmt-devel

For libStorageMgmt library code, please refer to [Library Developer Guide][12].

For libStorageMgmt plugin code, please refer to [C Plugin developer Guide][13]
or [Python Plugin developer Guide][14].

[1]: array_conf/NetApp_Conf.html
[2]: array_conf/EMC_Conf.html
[3]: array_conf/IBM_Conf.html
[4]: array_conf/LSI_Conf.html
[5]: array_conf/NetAppE_Conf.html
[6]: array_conf/SMIS_General_Conf.html
[7]: array_conf/TGT_Conf.html
[8]: array_conf/Nstor_Conf.html
[9]: py_api_user_guide.html
[10]: c_api_user_guide.html
[11]: https://freenode.net/
[12]: lib_dev_guide.html
[13]: c_plugin_dev_guide.html
[14]: py_plugin_dev_guide.html
[15]: install.html
