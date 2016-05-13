---
---

What problem we are trying to solve
---

Today system administrators utilizing open source solutions have no way
to programmatically manage their storage hardware in a vendor neutral
way.  This project's main goal is to provide this needed functionality.
To facilitate management automation, ease of use and take advantage of
storage vendor supported features which improve storage performance and
space utilization.

Proposed solution
---

A library that will provide a vendor agnostic open source storage
application programming interface (API) that will allow management of
storage arrays.

Deliverables
---
Provide a long term stable open source C library and bindings for other
computer languages.

Install package with the following
-----
* Necessary files for compilation
    * Header (.h) and shared objects (.so) for linking
    * Language bindings/libraries for each supported language
* Documentation (html, pdf, man pages)
    * Management API
    * Plug-in API
    * Language bindings


Initial features
---
 * Plug-in framework to facilitate storage array addition/integration
  * Allow proprietary plug-ins (open source encouraged)
 * Strong configurable security (TBD)
  * Username/password, client/server certificates
 * Interrogate/Query
  * Capabilities, limits, versioning
  * LUN listings
  * Initiator listing
  * Volume/Storage pool listing (Where to carve new logical disks)
 * Manage logical disks (LUNS)
  * creation (including thin provisioning)
  * deletion
  * re-sizing
  * access control (LUN Masking)
  * cloning/mirroring
  * snapshots
 * Command line interface for exercising API
 * Python bindings

Completed additional features
---
* Integrated support for LIO target (http://linux-iscsi.org)
* Network attached file systems (NFS)

Future features
---
 * Monitoring and alerting (asynchronous event notification)
    * Disk failure
    * Fan failure
    * Battery backup failure
    * RAID device has become degraded
    * Cache changes (write through changed to write back, so that FS must switch to issuing barriers)
    * Connection failure
    * Host port failure
    * Remote replication failure
    * Thin pool exhaustion
    * RAID synchronization complete
    * Thin provisioning threshold reached
    * RAID synchronization status, percent complete
 * Statistics gathering
    * Bandwidth/throughput per spindle/host port
    * Tiered storage "cache hits"
 * Network attached file systems (CIFS)
 * Management of SAN switches
 * Portable, availability for Linux, Solaris and Windows
 * Java, Perl, Ruby bindings
 * Manage local RAID adapters
    * Inquiry abilities
    * Display RAID health
    * Check on RAID memory backup battery health
    * Blink LED of physical disk
    * Retrieve SMART data for each physical disk
   * Change/control
    * Silence alarm
    * Rebuild array (Select hot-spare)
    * Create hot-spare

License
---
GNU Lesser General Public License (LGPL) reference
http://www.gnu.org/licenses/lgpl.html


