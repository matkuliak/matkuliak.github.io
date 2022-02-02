---
order: 6
---

UniConfig 4.2.8
===============

UniConfig
---------

**[NEW FEATURES]**

-   **UniConfig shell**: basic CRUD operations (configuration/operations
    mode), RPC calls, YANG actions.
-   **Validate RPC**: validation of NETCONF configuration by target
    device.
-   **Device discovery RPC**: searching for open TCP/UDP ports on target
    hosts ICMP reachability.

**[IMPROVEMENTS]**

-   Simplification of UniConfig RPCs in the transaction: RPCs
    (is-in-sync, commit, checked-commit,
    replace-config-with-operational, calculate-diff, sync-from-network,
    dryrun-commit) should work now with empty input. If the input is
    empty, operation will be invoked on all touched nodes.

**[FIXES]**

-   Unified representation of empty snapshot metadata - it will return
    404.
-   Propagation of southbound error message to Uniconfig layer after
    failed installation.

CONTROLLER
----------

**[NEW FEATURES]**

-   Auto-generation of local UniConfig instance name, if it is not set
    in the configuration file.

**[FIXES]**

-   **Fixed persistence of templates**: fixed extraction of node-id from
    path.
-   **Fixed omitting of module-name from URI**: skip
    openconfig/native-CLI augmentations from created UniConfig-native
    schema.
-   **Fixed parent module lookup when resolving leafrefs** - parent
    module was mapped not to parent, but the submodule itself.
-   **Fixed parsing of source-ids from YANG files** - don't inherit
    revision from parent module.

**[IMPROVEMENTS]**

-   Improved error message on failed building of schema context.
-   Optimized YANG schema cache: Removed in-memory schema cache listener
    that was caching bulky AST form of all sources. Caching of them is
    not valuable anymore because there is only 1 schema context per
    device-type.

SWAGGER
-------

**[FIXES]**

-   Removed trailing slash from generated URIs (conforming RFC-8040
    format).
-   Fixed importing of 4.0.0-alpha-1-SNAPSHOT (maven-core).

**[IMPROVEMENTS]**

-   Stop emitting operational nodes in swagger.
-   Adding snapshots-metadata and tx-log to generated swagger-api.

CLI
---

**[FIXES]**

-   Fixed initialization of SSH session: Enforced following order of
    messages in SSH client - Protocol (SSH-2.0-APACHE-SSHD-2.4.0),
    Protocol (SSH-2.0-Cisco-1.25), Key Exchange Init, Key Exchange Init
    (some devices don't accept switching Protocol and Key Exchange Init
    messages).
-   Fixed setting infinite number of reconnection attempts.

NETCONF
-------

**[NEW FEATURES]**

-   **NETCONF PKI data persistence**: persistence of crypto information
    in the file-system.

**[FIXES]**

-   Capturing error message from SSH session initialization process.
-   Fixed setting infinite number of reconnection attempts.
-   Fixed self-reconnection of NETCONF session (issue with keepalive
    timer).
-   Fixed netconf testtool in mdsal-persistent-mode - do not share
    Datastore across all devices.
-   Fixed overwriting IETF schemas by UniConfig shcemas in
    netconf-testtool.

**[IMPROVEMENTS]**

-   Removed unused netconf-ssh classes.
-   Improving the way of printing NETCONF reconnection attempts.
-   Testtool: Enable manipulation of operational data over NETCONF.

RESTCONF
--------

**[NEW FEATURES]**

-   **Pagination**: get-count, limit, and start-index query parameters.

**[FIXES]**

-   Fixed adding schema-respoitory parameter to PATCH operation.
-   Fixed serialization of identityref key value.

CLI TRANSLATION UNITS
---------------------

**[NEW FEATURES]**

-   **Huawei**: created TU for physical, VLAN interfaces,
    sub-interfaces.
-   **Huawei**: created TU for AAA.
-   **Huawei**: created TU for trunk and access VLANs.
-   **Huawei**: created TU for ACL.

**[FIXES]**

-   **IOS XE**: Fixed missing some information about route maps for IOS.
-   **IOS XE**: Fixed sending "dot1q 1-4094" to IOS XE devices.
-   **SAOS6**: Changed name for l2vlan interface to "cpu\_subintf\_"
    l2vlan name.
-   **SAOS6**: All interfaces cannot be marked as Ethernet.
-   **SAOS6**: Fixed creation of sub-port on EthernetCsmacd interfaces.

**[IMPROVEMENTS]**

-   **Huawei**: Add caching for "display current-configuration" command.
-   **Huawei**: Updated parsing of output for L3-VRF.
-   **Huawei**: Read interfaces of Huawei devices with "display
    interface brief".
-   **SAOS6**: Reading all interfaces from ciena devices using command
    "interface show".

NETCONF TRANSLATION UNITS
-------------------------

**[FIXES]**

-   Fixed importing ietf-inet-types - there are multiple revisions
    available in the UniConfig.

**[IMPROVEMENTS]**

-   Speed up device model build by disabling various maven plugins.

OPENCONFIG
----------

-   frinx-huawei-network-instance-extension - added network-instance
    extension.
-   frinx-saos-if-extension - added ipv4 and ipv6 address extension.
-   frinx-cisco-if-extension - the dot1q value type is changed from int
    to string and the range is saved as a string.
-   frinx-acl-extension - ACL for huawei devices
-   frinx-openconfig-aaa, frinx-openconfig-aaa-radius,
    frinx-openconfig-aaa-tacacs, frinx-openconfig-aaa-types,
    frinx-huawei-aaa-extension - added aaa and radius modules from
    openconfig.
-   frinx-huawei-if-extension - added yang for huawei interface and
    sub-interface extensions.
-   frinx-openconfig-bgp-types, frinx-openconfig-extensions -fixed bug
    with community set values.

