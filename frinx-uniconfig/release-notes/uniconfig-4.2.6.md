---
order: 4
---

UniConfig 4.2.6
===============

UniConfig:
----------

-   **new feature**: introduced 3-phase commit - integration of
    validation and confirmed-commit features -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/uniconfig-api/uniconfig-node-manager/rpc_commit/commit.html)
-   **new feature**: templates can be used for reusing of some
    configuration and afterwards easier application of this
    configuration into target UniConfig nodes - storing of templates in
    UniConfig, modification of templates including tags using RESTCONF
    operations, and application of templats to target UniConfig nodes
    using apply-template RPC
-   **new feature**: added copy-subtrees RPCs - merge or replace whole
    subtrees: copy-one-to-one, copy-one-to-many, copy-many-to-one
-   **new feature**: added calculate-subtree-diff RPC - calcution of
    diff between two subtress in datastore
-   **new feature**: implemented uniconfig healthcheck - RPC checks
    UniConfig and database connection
-   fixed auto-sync service
-   fixed creation of Unified mountpoint for CLI device without
    available translation units - using only 'generic' units in this
    case

CONTROLLER:
-----------

-   **improvement**: removed 'native\_prefix' from 'node' database
    relation - it is replaced by NETCONF repository name
-   fixed MDSAL union codec - it didn't work with boolean subtype

CLI:
----

-   **fixed unmounting of CLI device**: the case when mounting process
    hasn't successfully finished yet

NETCONF:
--------

-   **new feature**: NETCONF validate RPC and confirmed-commit RPC
    exposed by extension of DOM transaction
-   **improvement**: mounting NETCONF device with explicitly set NETCONF
    repository name that must be used - using this approach, it is not
    necessary to explicitly override/merge capabilities in the mount
    request -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/network-management-protocols/uniconfig_mounting/mounting-process.html#capabilities)
-   **improvement**: replacing uniconfig-native fingerprint by
    'schema-cache-directory' in NETCONF operational data
-   fixed mounting SROS device with specified
    ignoreNodes/namespaceBlacklist -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/network-management-protocols/uniconfig-native_netconf/sros.html#nokia-sros-devices)
-   **fixed**: unmounting of NETCONF device which mounting process
    hasn't finished yet
-   **fixed**: increased maximum NETCONF chunk size to 32\*1024\*1024

RESTCONF:
---------

-   **new feature**: introduced 'uniconfig-schema-repository' query
    parameter - explicitly set name of the schema using which
    input/output data is validated
-   **new feature**: JSON attributes - option to encode XML-like
    attributes into JSON structure: -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/restconf.html#json-attributes)

CLI TRANSLATION UNITS:
----------------------

-   **IOS**: fix - QoS translation unit, added port-channel into
    interface type
-   **IOS**: added translation units - storm-control, standard ACL
-   **IOS**: refactoring - allowed vlans on trunk interface
-   **SAOS**: fixed translation units - statistics augmentation, command
    ordering, ethernet config reader/writer, ordering of VLAN and VC,
    order of CPE commands
-   **SAOS**: fixed initialization - committing configuration during
    initialization

OPENCONFIG:
-----------

-   **frinx-acl-extension**: added support for standard ACL
-   moved statistics from frinx-saos-vlan-extension to
    frinx-saos-vc-extension
-   **frinx-cisco-if-extension**: added storm control
-   **frinx-qos-extension**: extended and fixed support for IOS QoS

Known Issues:
-------------

-   The error message needs to be fixed to inform user about the name
    clash and how to fix it
-   ODL did not started if cache folder for SROS16 device is applied

**BGP**: NullPointerException occurs when configure network instances
for XE

**NETCONF**: Junos 18 is can't be mounted by netconf Xrv6.2.3 device has
been locked and session went down after specific set of commands

**CLI**: Performance issues when is more than 400 devices connected

**RPC**: Commit and Checked commit issues when invalid configuration has
been applied to one router Transaction has been locked during checked
commit no rollback when invalid configuration has been configured to one
router
