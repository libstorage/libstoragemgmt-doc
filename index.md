---
title: Library for Storage Management
---

Today system administrators utilizing open source solutions have no way
to programmatically manage their storage hardware in a vendor neutral
way.  This project's main goal is to provide this needed functionality.
To facilitate management automation, ease of use and take advantage of
storage vendor supported features which improve storage performance and
space utilization.

**LibStorageMgmt** is:

* Community open source project.
* Free software under the [GNU Lesser General Public License][1].
* A long term stable C API.
* A set of bindings for common languages with initial support for
  [python][2].
* Supporting these actions:
    * List storage pools, volumes, access groups, or file systems.
    * Create and delete volumes, access groups, file systems, or NFS
      exports.
    * Grant and remove access to volumes, access groups, or initiators.
    * Replicate volumes with snapshots, clones, and copies.
    * Create and delete access groups and edit members of a group.
    * List Linux local SCSI/ATA/NVMe disks.
    * Control IDENT/FAULT LED of local disk via SES(SCSI Enclosure
      Service).

## Documentation

### General
* [Installation Guide][3]
* [User Guide][4]
* [Known issues][15]

### API User Guides
* [Python API User Guide][5]
* [C API User Guide][6]
* [Source code documentation][28]

### Developer Guide
* [Library Developer Guide][7]
* [C Plugin developer Guide][8]
* [Python Plugin developer Guide][17]
* [Proposed project scope][16]

### Array configurations
* [NetApp ONTAP][19]
* [EMC VMAX/DMX/VNX/CX][20]
* [Linux Targetd][21]
* [NexentaStor][22]
* [IBM XIV/DS/SVC][23]
* [NetApp E-Series][24]
* [Huawei HVS][25]
* [LSI MegaRAID][26]
* [HP SmartArray][27]
* [Microsemi SAS Controllers][29]

### Media & Papers
* [Slides from Linux Plumbers Conference 2012][9]
* [Youtube video: targetd and lsmcli: a demonstration][10]
* [Youtube video: NexentaStor plug-in demo][11]
* [White paper][12]

## Assistance
* IRC at #libStorageMgmt https://www.oftc.net/
* [Developer Mailing list][13]:
  libstoragemgmt-devel@lists.fedorahosted.org
* [User Mailing List][18]:
  libstoragemgmt-users@lists.fedorahosted.org
* [Bug report: Github Issue Page][14]

[1]: http://www.opensource.org/licenses/lgpl-license.html
[2]: http://python.org/
[3]: doc/install.html
[4]: doc/user_guide.html
[5]: doc/py_api_user_guide.html
[6]: doc/c_api_user_guide.html
[7]: doc/lib_dev_guide.html
[8]: doc/c_plugin_dev_guide.html
[9]: misc/LPC_lsm_2012.odp
[10]: http://www.youtube.com/watch?v=-ub25iAW9bE
[11]: http://www.youtube.com/watch?v=LYmY8NcQRlo
[12]: http://blog.asleson.org/index.php/2013/05/19/orchestrating-your-storage-libstoragemgmt/
[13]: https://lists.fedorahosted.org/mailman/listinfo/libstoragemgmt-devel
[14]: https://github.com/libstorage/libstoragemgmt/issues
[15]: doc/known_issues.html
[16]: doc/project_scope.html
[17]: doc/py_plugin_dev_guide.html
[18]: https://lists.fedorahosted.org/mailman/listinfo/libstoragemgmt-users
[19]: doc/array_conf/NetApp_Conf.html
[20]: doc/array_conf/EMC_Conf.html
[21]: doc/array_conf/TGT_Conf.html
[22]: doc/array_conf/Nstor_Conf.html
[23]: doc/array_conf/IBM_Conf.html
[24]: doc/array_conf/NetAppE_conf.html
[25]: doc/array_conf/Huawei_Conf.html
[26]: doc/array_conf/LSI_Conf.html
[27]: doc/array_conf/HPSA_Conf.html
[28]: doc/srcdoc/html/index.html
[29]: doc/array_conf/Arcconf_Conf.html
