---
title: Known issus
---

## MegaRAID Plugin
* VPD83 of volume is not supported yet.

    Due to limitation of storcli, we cannot provide VPD83 information of
    any volume.


## Targetd Plugin
* Only support iSCSI actions.

    Due to limitation of targetd, we cannot provide support of FC/FCoE,
    iSER target.

## SMI-S Plugin -- HDS

 * VPD83 is not supported on HDS AMS 2000 or older modular storage.

    HDS SMI-S provider for AMS 2000 or older modular storage does not provide
    VPD83 information, thus we cannot provide so via SMI-S plugin.
    This could be automatically solved once HDS provide such information in
    their SMI-S providers.
