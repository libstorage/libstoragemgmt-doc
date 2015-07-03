---
title: C API User Guide
---

* [1. Connection -- `lsm_connect`][01]
* [2. Capability -- `lsm_storage_capabilities`][02]
* [3. System -- `lsm_system`][03]
* [4. Pool -- `lsm_pool`][04]
* [5. Disk -- `lsm_pool`][05]
* [6. Volume -- `lsm_volume`][06]
* [7. Access Group -- `lsm_access_group`][07]
* [8. Volume Mask][08]
* [9. Volume Replication][09]
* [10. iSCSI Authentication][10]
* [Appendix.A. Asynchronous Job Control][aa]
* [Appendix.B. Bit Map][ab]
* [Appendix.C. Misc Functions and Structures][ac]
* [Appendix.D. Errors -- `lsm_error`][ad]

This document provides detail information about how to use
LibStorageMgmt C API for storage system management and assumes you have
read the [LibStorageMgmt User Guide][1].

The sample C code can be found at git repository [client_example.c][2].

To use LibStorageMgmt in your project:

* Install `libstoragemgmt-devel` package.

* Please add these lines in your `configure.ac`:

    ```text
    PKG_CHECK_MODULES(
        [LSM], [libstoragemgmt >= 1.0.2],,
        AC_MSG_ERROR([libstoragemgmt 1.0.2 or newer not found.]))
    ```
*  And similar lines below into your `Makefile.am` file:

    ```Makefile
    foo_CFLAGS = $(LSM_CFLAGS)
    foo_LDADD = $(LSM_LIBS)
    ```

* Make connection:

    ```c
    #include <libstoragemgmt/libstoragemgmt.h>

    lsm_connect *lsm_conn = NULL;
    lsm_error *lsm_err = NULL;
    int rc = 0;
    const char *lsm_uri = "sim://";
    const char *lsm_password = NULL;
    uint32_t lsm_tmo = 3000;

    rc = lsm_connect_password(
        lsm_uri, lsm_password, &lsm_conn, lsm_tmo, &lsm_err,
        LSM_CLIENT_FLAG_RSVD);
    ```

* Invoke LibStorageMgmt methods:

    ```c
    /** Use lsm_pool_list as example */
    int rc = 0;
    lsm_pool **lsm_pools = NULL;
    uint32_t lsm_pool_count = 0;

    rc = lsm_pool_list(
        lsm_conn, NULL, NULL, &lsm_pools, &lsm_pool_count,
        LSM_CLIENT_FLAG_RSVD);

    if( LSM_ERR_OK == rc ) {
        uint32_t i;
        for( i = 0; i < lsm_pool_count; ++i) {
            printf("pool name: %s freespace: %"PRIu64"\n",
                lsm_pool_name_get(pools[i]),
                lsm_pool_free_space_get(pools[i]));
        }

        lsm_pool_record_array_free(pools, count);
    } else {
        /** Handle error here */
    }
    ```

* Handle errors:

    Please refer to [Appendix.D. Errors][ad] for detail.

    These common errors might be returned by every methods:

    * `LSM_ERR_LIB_BUG`

    * `LSM_ERR_PLUGIN_BUG`

    * `LSM_ERR_TIMEOUT`

    * `LSM_ERR_INVALID_ARGUMENT`

    * `LSM_ERR_NETWORK_CONNREFUSED`

    * `LSM_ERR_NETWORK_HOSTDOWN`

    * `LSM_ERR_NETWORK_ERROR`

    * `LSM_ERR_NO_MEMORY`

    Sample code to handle errors:

    ```c
    lsm_error *lsm_err = lsm_error_last_get(lsm_conn);
    char *err_msg = NULL;
    if (lsm_err){
        printf("Error code: '%d'\n", lsm_error_number_get(lsm_err));
        err_msg = lsm_error_message_get(lsm_err);
        if (err_msg){
            printf("Error message: '%s'\n", err_msg);
        }
        lsm_error_free(lsm_err);
    }
    ```

## 1. Connection -- `lsm_connect`
* [1.1. Make Connection -- `lsm_connect_password`][0101]
* [1.2. Close Connection -- `lsm_connect_close`][0102]

The `lsm_connect` struct is an opaque data structure which holds
internal data, there is no public member in it.

### 1.1. Make Connection -- `lsm_connect_password`

```text
int lsm_connect_password(
    const char* uri, const char *password, lsm_connect **conn,
    uint32_t timeout, lsm_error_ptr *e, lsm_flag flags)

Version:
    1.0
Usage:
    Make the connection to LibStorageMgmt plugin.
Parameters:
    uri (input, const char*)
        URI string.
    password (input, const char*)
        Password string.
        If set to NULL pointer, it means no password is required.
    conn (output, lsm_connect **)
        Output pointer of lsm_connect type.
    timeout (input, uint32_t)
        Maximum milliseconds of runtime for each method before returning
        error with LSM_ERR_TIMEOUT. If set to 0, it means no timeout.
    e (output, lsm_error_ptr *)
        Output pointer of lsm_error_ptr type.
        Please refer to section [Appendix.D. Errors] for detail.
    flags (input, lsm_flag)
        Reserved for future use. Should be set as LSM_CLIENT_FLAG_RSVD.
Returns:
    rc (int)
        LSM_ERR_OK if connection was created without any error.
        When error found, please retrieve error information from output
        pointer argument 'e'.
        Please refer to section [Appendix.D. Errors] for detail.
Capability:
    No capability needed for this method.
SpecialErrors:
    LSM_ERR_PLUGIN_AUTH_FAILED
    LSM_ERR_PLUGIN_IPC_FAIL
    LSM_ERR_PLUGIN_SOCKET_PERMISSION
    LSM_ERR_PLUGIN_NOT_EXIST
    LSM_ERR_DAEMON_NOT_RUNNING
Sample:
    lsm_connect *lsm_conn = NULL;
    lsm_error *lsm_err = NULL;
    int rc = 0;
    const char *lsm_uri = "sim://";
    const char *lsm_password = NULL;
    uint32_t lsm_tmo = 3000;

    rc = lsm_connect_password(
        lsm_uri, lsm_password, &lsm_conn, lsm_tmo, &lsm_err,
        LSM_CLIENT_FLAG_RSVD);
```

### 1.2. Close Connection -- `lsm_connect_close`

```text
int lsm_connect_close(lsm_connect *conn, lsm_flag flags)

Version:
    1.0
Usage:
    Close the connection to LibStorageMgmt plugin.
Parameters:
    conn (input, lsm_connect *)
        The lsm_connect to close.
    flags (input, lsm_flag)
        Reserved for future use. Should be set as LSM_CLIENT_FLAG_RSVD.
Returns:
    rc (int)
        LSM_ERR_OK if connection was created without any error.
        Please refer to section [Appendix.D. Errors] for error
        number.
Capability:
    No capability needed for this method.
SpecialErrors:
        N/A
Sample:
    int rc = lsm_connect_close(lsm_conn, LSM_CLIENT_FLAG_RSVD);
```

## 2. Capability -- `lsm_storage_capabilities`

## 3. System -- `lsm_system`

## 4. Pool -- `lsm_pool`

* [4.4. Query Pool Membership -- `lsm_pool_member_info()`][0404]

Pool is the only place a volume or a file system could created from.

### 4.4. Query Pool Membership -- `lsm_pool_member_info()`

```text
int lsm_pool_member_info(
    lsm_connect *c, lsm_pool *pool, lsm_volume_raid_type *raid_type,
    lsm_pool_member_type *member_type, lsm_string_list **member_ids,
    lsm_flag flags);

Version:
    1.2
Usage:
    Query the membership information of certain pool:
        RAID type, member type and member ids.
    Currently, LibStorageMgmt supports two types of pool:
        * Sub-pool -- LSM_POOL_MEMBER_TYPE_POOL
            Pool space is allocated from parent pool.
            Example:
                * NetApp ONTAP volume

        * Disk RAID pool -- LSM_POOL_MEMBER_TYPE_DISK
            Pool is a RAID group assembled by disks.
            Example:
                * LSI MegaRAID disk group
                * EMC VNX pool
                * NetApp ONTAP aggregate
Parameters:
    c (input, lsm_connect *)
        The plugin connection.
    pool (input, lsm_pool *)
        The pool to query.
    raid_type (output, lsm_volume_raid_type *)
        The output pointer to lsm_volume_raid_type. Could be one of
        these values:
                LSM_VOLUME_RAID_TYPE_RAID0
                    Stripe
                LSM_VOLUME_RAID_TYPE_RAID1
                    Two disks Mirror
                LSM_VOLUME_RAID_TYPE_RAID3
                    Byte-level striping with dedicated parity
                LSM_VOLUME_RAID_TYPE_RAID4
                    Block-level striping with dedicated parity
                LSM_VOLUME_RAID_TYPE_RAID5
                    Block-level striping with distributed parity
                LSM_VOLUME_RAID_TYPE_RAID6
                    Block-level striping with two distributed parities,
                    aka, RAID-DP
                LSM_VOLUME_RAID_TYPE_RAID10
                    Stripe of mirrors
                LSM_VOLUME_RAID_TYPE_RAID15
                    Parity of mirrors
                LSM_VOLUME_RAID_TYPE_RAID16
                    Dual parity of mirrors
                LSM_VOLUME_RAID_TYPE_RAID50
                    Stripe of parities
                LSM_VOLUME_RAID_TYPE_RAID60
                    Stripe of dual parities
                LSM_VOLUME_RAID_TYPE_RAID51
                    Mirror of parities
                LSM_VOLUME_RAID_TYPE_RAID61
                    Mirror of dual parities
                LSM_VOLUME_RAID_TYPE_JBOD
                    Just bunch of disks, no parity, no striping.
                LSM_VOLUME_RAID_TYPE_UNKNOWN
                    The plugin failed to detect the volume's RAID type.
                LSM_VOLUME_RAID_TYPE_MIXED
                    This pool contains multiple RAID settings.
                LSM_VOLUME_RAID_TYPE_OTHER
                    Vendor specific RAID type
    member_type (output, lsm_pool_member_type *)
        Output pointer of lsm_pool_member_type.
        Could be one of these values:
            LSM_POOL_MEMBER_TYPE_POOL
                Current pool(also known as sub-pool) is allocated from
                other pool(parent pool). The 'raid_type' will set to
                LSM_VOLUME_RAID_TYPE_OTHER unless certain RAID system
                support RAID using space of parent pools.
            LSM_POOL_MEMBER_TYPE_DISK
                Pool created from RAID group using whole disks.
            LSM_POOL_MEMBER_TYPE_OTHER
                Vendor specific RAID member type.
            LSM_POOL_MEMBER_TYPE_UNKNOWN
                Plugin failed to detect the RAID member type.
    member_ids (output, lsm_string_list **)
        Output pointer of lsm_string_list.
        When 'member_type' is LSM_POOL_MEMBER_TYPE_POOL,
        the 'member_ids' will contain a string list of parent Pool IDs.
        When 'member_type' is LSM_POOL_MEMBER_TYPE_DISK,
        the 'member_ids' will contain a string list of disk IDs.
        When 'member_type' is LSM_POOL_MEMBER_TYPE_OTHER or
        LSM_POOL_MEMBER_TYPE_UNKNOWN, the '*member_ids' will be set
        as NULL.
        Please refer to [Appendix.C.1 String Array] section for
        'lsm_string_list' structure members.
        This memory requires manually free via lsm_string_list_free().
    flags (input, lsm_flag)
        Reserved for future use. Should be set as LSM_CLIENT_FLAG_RSVD.
Returns:
    rc (int)
        LSM_ERR_OK if connection was created without any error.
        Please refer to section [Appendix.D. Errors] for detail.
Capability:
    LSM_CAP_POOL_MEMBER_INFO
SpecialErrors:
    LSM_ERR_NO_SUPPORT
    LSM_ERR_NOT_FOUND_POOL
Sample:
    lsm_volume_raid_type raid_type;
    lsm_pool_member_type member_type;
    lsm_string_list *member_ids = NULL;
    uint32_t i;
    int rc;

    rc = lsm_pool_member_info(
        c, pool, &raid_type, &member_type, &member_ids,
        LSM_CLIENT_FLAG_RSVD);
    if ( rc == LSM_ERR_OK ){
        for(i = 0; i < lsm_string_list_size(member_ids); i++){
            // Simulator user reading the member id.
            const char *cur_member_id = lsm_string_list_elem_get(
                member_ids, i);
            printf("Member ID: %s\n", cur_member_id);
        }
        lsm_string_list_free(member_ids);
        lsm_pool_record_free(pool);
    }
```

## 5. Disk -- `lsm_pool`

## 6. Volume -- `lsm_volume`

* [6.9. Query Volume RAID Information
  -- `lsm_volume_raid_info()`][0609]
* [6.10. Check RAID Volume Creation Capability --
  `lsm_volume_raid_create_cap_get())`][0610]
* [6.11. Create RAID volume -- `lsm_volume_raid_create()`][0611]

Volume is well-known as LUN by many storage array vendors. It is a
virtual storage space which host operation system treated as a or many
block device(s).

### 6.9. Query Volume RAID Information -- `lsm_volume_raid_info()`

```text
int lsm_volume_raid_info(
    lsm_connect *c, lsm_volume *volume, lsm_volume_raid_type *raid_type,
    uint32_t *strip_size, uint32_t *disk_count,
    uint32_t *min_io_size, uint32_t *opt_io_size, lsm_flag flags)

Version:
    1.2
Usage:
    Query the RAID information of certain volume.
    Query the RAID type, strip size, extents count, minimum I/O size,
    optimal I/O size of given volume.
Parameters:
    c (input, lsm_connect *)
        The plugin connection.
    volume (input, lsm_volume *)
        The volume to query.
    raid_type (output, lsm_volume_raid_type *)
        The output pointer to lsm_volume_raid_type. Could be one of
        these values:
                LSM_VOLUME_RAID_TYPE_RAID0
                    Stripe
                LSM_VOLUME_RAID_TYPE_RAID1
                    Two disks Mirror
                LSM_VOLUME_RAID_TYPE_RAID3
                    Byte-level striping with dedicated parity
                LSM_VOLUME_RAID_TYPE_RAID4
                    Block-level striping with dedicated parity
                LSM_VOLUME_RAID_TYPE_RAID5
                    Block-level striping with distributed parity
                LSM_VOLUME_RAID_TYPE_RAID6
                    Block-level striping with two distributed parities,
                    aka, RAID-DP
                LSM_VOLUME_RAID_TYPE_RAID10
                    Stripe of mirrors
                LSM_VOLUME_RAID_TYPE_RAID15
                    Parity of mirrors
                LSM_VOLUME_RAID_TYPE_RAID16
                    Dual parity of mirrors
                LSM_VOLUME_RAID_TYPE_RAID50
                    Stripe of parities LSM_VOLUME_RAID_TYPE_RAID60
                    Stripe of dual parities
                LSM_VOLUME_RAID_TYPE_RAID51
                    Mirror of parities
                LSM_VOLUME_RAID_TYPE_RAID61
                    Mirror of dual parities
                LSM_VOLUME_RAID_TYPE_JBOD
                    Just bunch of disks, no parity, no striping.
                LSM_VOLUME_RAID_TYPE_UNKNOWN
                    The plugin failed to detect the volume's RAID type.
                LSM_VOLUME_RAID_TYPE_MIXED
                    This pool contains multiple RAID settings.
                LSM_VOLUME_RAID_TYPE_OTHER
                    Vendor specific RAID type
    strip_size (output, uint32_t *)
        Output pointer of uint32_t.
        The size of strip on each disk or other storage extent.
        For RAID1/JBOD, it should be set as sector size.
        If plugin failed to detect strip size, it should be set
        as LSM_VOLUME_STRIP_SIZE_UNKNOWN(0).
    disk_count (output, uint32_t *)
        Output pointer of uint32_t.
        The count of disks used for assembling the RAID group(s) where
        this volume allocated from. For any RAID system using the slice
        of disk, this value indicate how many disk slices are used for
        the RAID.  For exmaple, on LVM RAID, the 'disk_count' here
        indicate the count of PVs used for certain volume.  Another
        example, on EMC VMAX, the 'disk_count' here indicate how many
        hyper volumes are used for this volume. For any RAID system
        using remote LUN for data storing, each remote LUN should be
        count as a disk.  If the plugin failed to detect disk_count, it
        should be set as LSM_VOLUME_DISK_COUNT_UNKNOWN(0).
    min_io_size (output, uint32_t *)
        Output pointer of uint32_t.
        The minimum I/O size, device preferred I/O size for random I/O.
        Any I/O size not equal to a multiple of this value may get
        significant speed penalty. Normally it refers to strip size of
        each disk(extent). If plugin failed to detect min_io_size, it
        should try these values in the sequence of:
            logical sector size -> physical sector size
            -> LSM_VOLUME_MIN_IO_SIZE_UNKNOWN(0).
    opt_io_size (output, uint32_t *)
        Output pointer of uint32_t.
        The optimal I/O size, device preferred I/O size for sequential
        I/O. Normally it refers to RAID group stripe size. If plugin
        failed to detect opt_io_size, it should be set to
        LSM_VOLUME_OPT_IO_SIZE_UNKNOWN(0).
    flags (input, lsm_flag)
        Reserved for future use. Should be set as LSM_CLIENT_FLAG_RSVD.
Returns:
    rc (int)
        LSM_ERR_OK if connection was created without any error.
        Please refer to section [Appendix.D. Errors] for detail.
Capability:
    LSM_CAP_VOLUME_RAID_INFO
SpecialErrors:
    LSM_ERR_NO_SUPPORT
    LSM_ERR_NOT_FOUND_VOLUME
Sample:
    lsm_volume_raid_type raid_type;
    lsm_pool_member_type member_type;
    lsm_string_list *member_ids = NULL;
    uint32_t i;
    int rc;

    rc = lsm_pool_member_info(
        c, pool, &raid_type, &member_type, &member_ids,
        LSM_CLIENT_FLAG_RSVD);
    if ( rc == LSM_ERR_OK ){
        for(i = 0; i < lsm_string_list_size(member_ids); i++){
            // Simulator user reading the member id.
            const char *cur_member_id = lsm_string_list_elem_get(
                member_ids, i);
            printf("Member ID: %s\n", cur_member_id);
        }
        lsm_string_list_free(member_ids);
        lsm_pool_record_free(pool);
    }
```

### 6.10. Check RAID Volume Creation Capability -- `lsm_volume_raid_create_cap_get()`

```text
int lsm_volume_raid_create_cap_get(
    lsm_connect *c, lsm_system *system, uint32_t **supported_raid_types,
    uint32_t *supported_raid_type_count,
    uint32_t **supported_strip_sizes,
    uint32_t *supported_strip_size_count, lsm_flag flags)

Version:
    1.2
Usage:
    This method is dedicated to local hardware RAID cards.
    Query out all supported RAID types and strip sizes which could
    be used by lsm_volume_raid_create() method.
Parameters:
    c (input, lsm_connect *)
        The plugin connection.
    system (input, lsm_system *)
        The system to query.
    supported_raid_types (output, uint32_t **)
        The output pointer to uint32_t array.
        Could contain any of these values:
                LSM_VOLUME_RAID_TYPE_RAID0
                LSM_VOLUME_RAID_TYPE_RAID1
                LSM_VOLUME_RAID_TYPE_RAID5
                LSM_VOLUME_RAID_TYPE_RAID6
                LSM_VOLUME_RAID_TYPE_RAID10
                LSM_VOLUME_RAID_TYPE_RAID50
                LSM_VOLUME_RAID_TYPE_RAID60
        If no RAID type is supported, the '*supported_raid_types' will
        be set as NULL.
        This memory requires manuall free via free().
    supported_raid_type_count (output, uint32_t *)
        The output pinter of uint32_t.
        The size of '*supported_raid_types' uint32_t array.
        If no RAID type is supported, the '*supported_raid_type_count'
        will be set as 0.
    supported_strip_sizes (output, uint32_t **)
        The output pointer to uint32_t array.
        Array of strip size in bytes.
        If no strip size is supported, the '*supported_strip_sizes'
        will be set as NULL.
        This memory requires manuall free via free().
    supported_strip_size_count (output, uint32_t *)
        The output pinter of uint32_t.
        The size of '*supported_strip_sizes' uint32_t array.
        If no strip size is supported, the '*supported_strip_size_count'
        will be set as 0.
    flags (input, lsm_flag)
        Reserved for future use. Should be set as LSM_CLIENT_FLAG_RSVD.
Returns:
    rc (int)
        LSM_ERR_OK if connection was created without any error.
        Please refer to section [Appendix.D. Errors] for detail.
Capability:
    LSM_CAP_VOLUME_RAID_CREATE
SpecialErrors:
    LSM_ERR_NO_SUPPORT
    LSM_ERR_NOT_FOUND_SYSTEM
Sample:
    uint32_t *supported_raid_types = NULL;
    uint32_t supported_raid_type_count = 0;
    uint32_t *supported_strip_sizes = NULL;
    uint32_t supported_strip_size_count = 0;
    int rc;
    uint32_t i;

    rc = lsm_volume_raid_create_cap_get(
        c, sys, &supported_raid_types, &supported_raid_type_count,
        &supported_strip_sizes, &supported_strip_size_count,
        LSM_CLIENT_FLAG_RSVD);

    for (i=0; i < supported_raid_type_count; ++i){
        // check if RAID 5 is supported
        if ( LSM_VOLUME_RAID_TYPE_RAID5 == supported_raid_types[i] ){
            printf("Create RAID 5 volume is supported\n";
            break;
        }
    }

    for (i=0; i < supported_strip_size_count; ++i){
        // check if 64 KiB strip is supported
        if ( 64 * 1024 == supported_strip_sizes[i] ){
            printf("Create 64KiB strip RAID volume is supported\n";
            break;
        }
    }

    free(supported_raid_types);
    free(supported_strip_sizes);
```

### 6.11. Create RAID volume -- `lsm_volume_raid_create()`

```text
int lsm_volume_raid_create(
    lsm_connect *c, const char *name, lsm_volume_raid_type raid_type,
    lsm_disk *disks[], uint32_t disk_count, uint32_t strip_size,
    lsm_volume **new_volume, lsm_flag flags);

Version:
    1.2
Usage:
    This method is dedicated to local hardware RAID cards.
    Create a disk RAID pool and allocate entire storage space to
    new volume using requested volume name.
    When dealing with RAID10, 50 or 60, the first half part of
    'disks' will be located in one bottom layer RAID group.
    The new volume and new pool will created within the same system
    of provided disks.
    This method does not allow duplicate call, if duplicate call
    was issue, LSM_ERR_DISK_NOT_FREE will be returned.
    User should check lsm_disk_status_get() for LSM_DISK_STATUS_FREE
    before invoking this method.
Parameters:
    c (input, lsm_connect *)
        The plugin connection.
    name (input, const char *)
        The pointer of String.
        The name for new volume.
        The requested volume name might be ignored due to restriction
        of hardware RAID vendors.
        The pool name will be automatically chose by plugin.
    raid_type (input, lsm_volume_raid_type *)
        The pointer to lsm_volume_raid_type.
        The possible values are:
            LSM_VOLUME_RAID_TYPE_RAID0
            LSM_VOLUME_RAID_TYPE_RAID1
            LSM_VOLUME_RAID_TYPE_RAID5
            LSM_VOLUME_RAID_TYPE_RAID6
            LSM_VOLUME_RAID_TYPE_RAID10
            LSM_VOLUME_RAID_TYPE_RAID50
            LSM_VOLUME_RAID_TYPE_RAID60
        Please check lsm_volume_raid_create_cap_get() returns to get
        supported all raid types of current hardware RAID card.
    disks (input, lsm_disk **)
        The pinter of lsm_disk pointer array.
        The free disks for new disk RAID pool.
    disk_count (input, uint32_t)
        The size of lsm_disk pointer array.
    strip_size (input, uint32_t)
        Strip size in bytes.
        When setting strip_size to LSM_VOLUME_VCR_STRIP_SIZE_DEFAULT, it
        allows hardware RAID cards to choose their default value.
        Please use lsm_volume_raid_create_cap_get() method to get all
        supported strip size of current hardware RAID card.
        The LSM_VOLUME_VCR_STRIP_SIZE_DEFAULT is always supported when
        LSM_CAP_VOLUME_CREATE_RAID is supported.
    new_volume (output, lsm_volume **)
        The output pinter of lsm_volume pointer.
        This memory requires manuall free via lsm_pool_record_free().
    flags (input, lsm_flag)
        Reserved for future use. Should be set as LSM_CLIENT_FLAG_RSVD.
Returns:
    rc (int)
        LSM_ERR_OK if connection was created without any error.
        Please refer to section [Appendix.D. Errors] for detail.
Capability:
    LSM_CAP_VOLUME_RAID_CREATE
SpecialErrors:
    LSM_ERR_NO_SUPPORT
    LSM_ERR_NOT_FOUND_DISK
    LSM_ERR_DISK_NOT_FREE
        Disk is not in LSM_DISK_STATUS_FREE status.
    LSM_ERR_INVALID_ARGUMENT
        1. Invalid input argument data.
        2. Disks are not from the same system.
        3. Disks are not from the same enclosure.
        4. Invalid strip_size.
        5. Disk count are meet the minimum requirement:
            RAID1: len(disks) == 2
            RAID5: len(disks) >= 3
            RAID6: len(disks) >= 4
            RAID10: len(disks) % 2 == 0 and len(disks) >= 4
            RAID50: len(disks) % 2 == 0 and len(disks) >= 6
            RAID60: len(disks) % 2 == 0 and len(disks) >= 8
    LSM_ERR_NAME_CONFLICT
        Requested name is already be used by other volume.
Sample:
    // in example of lsm_volume_raid_create_cap_get(),
    // we already confirmed RAID 5 with strip 64 KiB is supported.

    lsm_disk **disks = NULL;
    uint32_t disk_count = 0;
    uint32_t rc;
    uint32_t free_disk_count = 0;
    lsm_disk *free_disks[3];
    lsm_volume *new_volume = NULL;

    // Find out three free disks
    rc = lsm_disk_list(
        c, NULL, NULL, &disks, &disk_count, LSM_CLIENT_FLAG_RSVD);

    if( rc == LSM_ERR_OK ){
        for (i = 0; i< disk_count; i++){
            if (lsm_disk_status_get(disks[i]) & LSM_DISK_STATUS_FREE){
                free_disks[free_disk_count++] = disks[i];
                if (free_disk_count == 3){
                    break;
                }
            }
    }else{
        // Handle error of lsm_disk_list()
    }

    if( free_disk_count != 3 ){
        lsm_disk_record_array_free(disks, disk_count);
        printf("Not enough(3) free disks to create RAID 5\n";
        exit(1);
    }

    rc = lsm_volume_raid_create(
        c, "test_volume_raid_create",
        LSM_VOLUME_RAID_TYPE_RAID5, free_disks, free_disk_count,
        64 * 1024, &new_volume, LSM_CLIENT_FLAG_RSVD);

    if( rc == LSM_ERR_OK ){
        printf("New volume %s created\n", lsm_volume_id_get(new_volume);
        lsm_volume_record_free(new_volume);
        lsm_disk_record_array_free(disks, disk_count);
    }else{
        // Handle error of lsm_volume_raid_create()
    }
```

## 7. Access Group -- `lsm_access_group`

## 8. Volume Mask

## 9. Volume Replication

## 10. iSCSI Authentication

## Appendix.A. Asynchronous Job Control

## Appendix.B. Bit Map

## Appendix.C. Misc Functions and Structures

### Appendix.C.1 String Array -- `lsm_string_list`
* [Appendix.C.1.1 `lsm_string_list_alloc()`][ac0101]
* [Appendix.C.1.2 `lsm_string_list_free()`][ac0102]
* [Appendix.C.1.3 `lsm_string_list_copy()`][ac0103]
* [Appendix.C.1.4 `lsm_string_list_elem_set()`][ac0104]
* [Appendix.C.1.5 `lsm_string_list_elem_get()`][ac0105]
* [Appendix.C.1.6 `lsm_string_list_size()`][ac0106]
* [Appendix.C.1.7 `lsm_string_list_append()`][ac0107]
* [Appendix.C.1.8 `lsm_string_list_delete()`][ac0108]

#### Appendix.C.1.1 `lsm_string_list_alloc()`
#### Appendix.C.1.2 `lsm_string_list_free()`
#### Appendix.C.1.3 `lsm_string_list_copy()`
#### Appendix.C.1.4 `lsm_string_list_elem_set()`
#### Appendix.C.1.5 `lsm_string_list_elem_get()`
#### Appendix.C.1.6 `lsm_string_list_size()`
#### Appendix.C.1.7 `lsm_string_list_append()`
#### Appendix.C.1.8 `lsm_string_list_delete()`

## Appendix.D. Errors -- `lsm_error`

[01]: #1-connection-lsm_connect
[0101]: #1-1-make-connection-lsm_connect_password
[0102]: #1-2-close-connection-lsm_connect_close
[02]: #2-capability-lsm_storage_capabilities
[03]: #3-system-lsm_system
[04]: #4-pool-lsm_pool
[0404]: #4-4-query-pool-membership-lsm_pool_member_info
[05]: #5-disk-lsm_pool
[06]: #6-volume-lsm_volume
[0609]: #6-9-query-volume-raid-information-lsm_volume_raid_info
[0610]: #6-10-check-raid-volume-creation-capability-lsm_volume_raid_create_cap_get
[0611]: #6-11-create-raid-volume-lsm_volume_raid_create
[07]: #7-access-group-lsm_access_group
[08]: #8-volume-mask
[09]: #9-volume-replication
[10]: #10-iscsi-authentication
[aa]: #appendix-a-asynchronous-job-control
[ab]: #appendix-b-bit-map
[ac]: #appendix-c-misc-functions-and-structures
[ac01]: #appendix-c-1-string-array-lsm_string_list
[ac0101]: #appendix-c-1-1-lsm_string_list_alloc
[ac0102]: #appendix-c-1-2-lsm_string_list_free
[ac0103]: #appendix-c-1-3-lsm_string_list_copy
[ac0104]: #appendix-c-1-4-lsm_string_list_elem_set
[ac0105]: #appendix-c-1-5-lsm_string_list_elem_get
[ac0106]: #appendix-c-1-6-lsm_string_list_size
[ac0107]: #appendix-c-1-7-lsm_string_list_append
[ac0108]: #appendix-c-1-8-lsm_string_list_delete
[ad]: #appendix-d-errors-lsm_error
