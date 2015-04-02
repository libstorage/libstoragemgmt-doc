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

## Documentation

### General
* [Installation Guide][3]
* [User Guide][4]
* [Known issues][15]

### API User Guides
* [Python API User Guide][5]
* [C API User Guide][6]

### Developer Guide
* [Library Developer Guide][7]
* [C Plugin developer Guide][8]
* [Python Plugin developer Guide][17]
* [Proposed project scope][16]

### Media & Papers
* [Slides from Linux Plumbers Conference 2012][9]
* [Youtube video: targetd and lsmcli: a demonstration][10]
* [Youtube video: NexentaStor plug-in demo][11]
* [White paper][12]

## Assistance
* IRC at #libStorageMgmt http://freenode.net
* [Developer Mailing list][13]:
  libstoragemgmt-users@lists.fedorahosted.org
* [User Mailing List][18]:
  libstoragemgmt-devel@lists.fedorahosted.org
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
