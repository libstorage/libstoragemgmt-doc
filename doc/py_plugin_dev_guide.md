---
---

# LibstorageMgmt Plugin Developer Guide

This document is assuming you already read the [v1_user_guide].

The LSM plugin codes are located

## 1. Workflow.

Each plugin has its own folder in "libstoragemgmt-code/plugin' folder.
We suggest use the plugin name as folder name.

Each plugin will has a excutable file named as 'xxx_lsmplugin' (xxx is the
name of plugin). For example: 'abc_lsmplugin' will be excuted by lsmd when
client is calling with URI 'abc://....'.

The lsmd already created a socket for plugin to communicate with client.
The socket file descriptor will be the first argument when lsmd excuting
'abc_lsmplugin'.

The 'abc_lsmplugin' will communicate with client in JSON.

We created plugin helper/runner to unifiy the code for socket handling, JSON
converting and etc.

## 2. C Plugin

### 2.1 C Plugin helper

TODO: List all methods dedicate for plugin use.

### 2.2 Linking with LSM

## 3. Python Plugin

### 2.1 Python plugin helper

TODO: List all methods dedicate for plugin use.

TODO: Provide sample for xxx_lsmplugin

## 4. Installation files for new plugin

### 4.1 Makefile

Sample changes to Makefiles for shipping new plugin.

### 4.2 RPM SPEC -- packaging/libstoragemgmt.spec.in

Sample changes to rpm spec file for shipping new plugin in rpm way.

## 5. Sample C plugin -- simc

TODO: Help new commer understand how simc works.

## 6. Sample Python plugin -- sim

The 'sim' plugin contains three python files:

    * sim_lsmplugin
      Handing the socket creation and JSON communication.
    * simulator.py
      Handling lsm.Client methods.
    * simarray.py
      Handling intenral data storage.

The 'sim' plugin will store/load data into/from file(default is
/tmp/lsm_sim_data) using 'pickle' python module.

There are four classes in this simulator plugin:
    * SimPlugin
      Talk to lsm.Client (python_binding/lsm/_client.py)
    * SimArray
      Talk to SimPlugin, handling internal data load/save.
    * SimData
      Talk to SimArray. Handling internal data
      management(create/update/delete).
    * SimJob
      Talk to SimData. Simulating ASYNC jobs.

Data is stored in a dictionary by SimData class.
For example, pools are stored as:

    self.pool_dict = {
        Pool.id = sim_pool,
    }
    sim_pool = {
        'name': Pool.name,
            .
            .
            .
    }


## 7. Plugin test

Plugin test is located at "libstoragemgmt-code/test/plugin_test.py".

TODO: explain how to use it.


