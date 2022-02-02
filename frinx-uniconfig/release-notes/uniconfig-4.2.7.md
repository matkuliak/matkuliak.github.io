---
order: 5
---

UniConfig 4.2.7
===============

Uniconfig
---------

**[NEW FEATURES]**

-   Implementation of transaction-tracker (transaction-log): tracking of
    successfully committed data.
-   Implementation of revert-changes RPC: reverting transaction that is
    stored in transaction-log and identified by unique UUID.
-   Introduction of UniConfig transactions - dedicated/shared
    transactions concept: multiple users can use UniConfig safely from
    isolated transactions. UniConfig RPCs are part of UniConfig
    transactions - information about transaction-id is passed from the
    RESTCONF layer into the UniConfig layer.
-   Introduction of install-node, uninstall-node, mount-node, and
    unmount-node RPCs - a new way to install nodes into UniConfig with
    split concepts of installation and mounting. Mounting is always done
    on demand and the mountpoint is alive as long as some transaction is
    using this mountpoint.
-   Making UniConfig instance stateless - data is separated from
    UniConfig (PostgreSQL database) and UniConfig doesn't keep
    persistent connection to devices. Data and connection recovery is
    not done by UniConfig instances anymore (coordination, monitoring,
    and recovery process is not orchestrated by UniConfig). From the
    view of data-tree, UniConfig is used only as a cache layer on top of
    PostgreSQL database and caching is done only in the scope of
    transaction.
-   Using commit RPC for committing snapshots and templates.
-   Using distributed advisory locks provided by PostgreSQL for locking
    of UniConfig nodes during commit/checked-commit operation. If
    another transaction perfors commit at the same time, it will fail
    before execution of the second commit.
-   Validation of conflicts between different transactions: added
    data-tree and config fingerprint validation before commit /
    checked-commit RPC invocation.
-   Integration of fingerprint validation to templates - writing of
    fingerprint of modified templates to database and verification of
    fingerprint before commit.
-   Added UniConfig transaction-id as fingerprint for devices not
    supporting it.
-   UniConfig shell prototype: SSH server, RPC operations, simple read
    operation.
-   Implementation of get-installed-nodes RPC: used for listing
    installed UniConfig nodes.

**[IMPROVEMENTS]**

-   Removed unused Karaf features.
-   Handling reordering of list entries in the calculate-diff - instead
    of sending delete+replace operations to the southbound layer.
-   Improving the existing algorithm that collapses diff from honeycomb
    (parallel streams).
-   Improved error messages during application of template.
-   Improved error messages - using serialized form of
    YangInstanceIdentifier in logs or error messages, if possible.
-   Removed data-tree cache layers on CLI and NETCONF layers - UniConfig
    directly writes data to CLI/NETCONF mountpoints - it simplifies
    syncing process too.
-   Adjusted persistence of mount information - node with the same ID
    may be present in both CLI/NETCONF topologies - and node only from
    one topology at the same time can be used for installation on
    UniConfig layer (configuration is synced and parsed).
-   Changed native-CLI architecture - UniConfig calls native-CLI
    readers/writers directly using BI API - BA translation layer
    provided by Honeycomb is redundant.
-   Removed snapshot limit - it is not used anymore since snapshots are
    stored in the database and this database should manage its storage
    limits.

**[FIXES]**

-   Fixed rollback operation after commit/checked-commit.
-   Fixed sync-from-network for unavailable nodes - Comparison of config
    fingerprints failed for nodes that are unavailable because reading
    of fingerprint failed.
-   Fixed version-drop in copy RPC.
-   Fixed writing ordered-map nodes during string substitution process
    (application of template).
-   Fixed commit output: if the configuration of one of the nodes fails
    at any phase, then the outputs for all nodes will always contain a
    rollback flag.
-   Fixed losing of some tags in DOM nodes (application of template)
-   Fixed dry-run commit - Dry-run commit should trash journal of nodes
    that haven't been 'touched'.
-   Fixed calculate-diff - Removing the whole list node with all list
    entries.
-   Fixed transfering of template tag from template to uniconfig
    topology at apply-template RPC (it should not happen).
-   Mark sync operation failed on empty config.
-   Fixed reading of uniconfig-native flag - unboxing of null Boolean to
    boolean.
-   Fixed creation/removal of dry-run Unified mountpoint -
    synchronization problems.

Controller
----------

**[NEW FEATURES]**

-   Integration of UniConfig transaction manager with database and
    datastore transactions - used for management of shared/dedicated
    transactions.
-   Integration of JSONB filtering of configuration on the level of
    DAOs.
-   Persistence of snapshots in PostgreSQL.
-   Persistence of logging configuration in PostgreSQL.
-   Persistence of templates in PostgreSQL.
-   Persistence of transaction-log in PostgreSQL.
-   Separated persistence of UniConfig nodes and representing
    mountpoints.
-   Validation and locking of templates and UniConfig nodes on the level
    of UniConfig transaction.
-   Stop submitting datastore transactions - it must be closed -
    datastore is used only as cache.
-   Introduction of embedded PostgreSQL for testing purposes - it can be
    enabled from the UniConfig configuration file.
-   Integration of Flyway library to Uniconfig: easier upgrading of
    database schema and migration of data.
-   Implemented standalone DOM broker - stopping to use
    clustered/distributed DOM brokers.
-   Extending RPC service by custom parameters that can be passed from
    RPC caller to RPC implementation.
-   Exposed simple container merge utility.
-   Allow users to specify attributes without module-name (template
    tags).
-   Allow positional information in YangInstanceIdentifier (useful for
    operations under ordered lists).

**[FIXES]**

-   Fixed order in which database writers are called (adding priority to
    DatabaseWriter API).
-   Fixed creation of DocumentedException from XML (document may include
    redundant namespaces).
-   Fixed leaked DB connection on health-check operation.
-   Making the database layer more thread safe (using 'SELECT FOR
    UPDATE' in some queries).
-   Fixed race-conditions in 3-phase datastore commit.
-   Fixed searching for fallback context on nodes that were not mounted
    (uniconfig-native).
-   Added synchronization when generating BA-\>BI codecs.
-   Fixed storing of the default schema repository into PostgreSQL.
-   Fixed disappeared tag from template data-tree.
-   Fixed data-tree modifications: merge-\>put-\>delete operation chain.
-   Added workaround for 'metadata not available' data-tree bug.

**[IMPROVEMENTS]**

-   Removed unused Karaf features.
-   Ensuring parents by merge: avoiding ridiculous errors when data-tree
    allows to write data to nodes which parent is missing.
-   Generalisation of NETCONF repository into YANG repository.
-   Replaced asynchronous DB API by synchronous DB API - JDBC
    connections are synchronous.
-   Breaking PUT modifications to specific modifications in the
    data-tree: improving 'optimistic lock' granularity.
-   Removed unnecessary dependencies of xtend maven plugin.
-   Optimized creation of uniconfig-native schemas.
-   Preserving order of list/leaf-leaf elements in the data-tree.

Swagger
-------

**[FIXES]**

-   Fixed bug caused by swagger-uniconfig-go.

**[IMPROVEMENTS]**

-   Make openAPI generated for uniconfig more useful.
-   Added Unified layer models to swagger dependencies.

Translation units framework
---------------------------

**[NEW FEATURES]**

-   Added native-CLI binding-independent API.

**[IMPROVEMENTS]**

-   Removed unused artifacts.
-   Optimized chunk cache - do not store entire writer in chunk cache,
    so GC can take care of writers as soon as possible.
-   Detection of complex reordering of list entries in diff output.

**[FIXES]**

-   Fixed commit rollback failing: the bug was caused by an attempt to
    execute an inverse command of an unsuccessful command.

CLI
---

**[IMPROVEMENTS]**

-   Removed unused Karaf features.
-   Exposed binding-independent data support to native-CLI API.
-   Exposed services for direct device access to MP.

**[FIXES]**

-   Replace maxConnectionAttempts with maxReconnectionAttempts when
    reconnecting to the device after the first connection attempt is
    successful.
-   Replaced transactionChain (not working correctly) with direct
    dataBroker transactions.
-   Fixed device type checking - when a device was mounted with the
    wrong type, the generic symbol ("*") was implicitly used as the
    type. The device was installed on all layers, but
    uniconfig/configuration was empty. Now we have to use correct device
    type or*.
-   Fixed disabled CLI journaling (default value).

NETCONF
-------

**[NEW FEATURES]**

-   Added maxReconnectionAttempts functionality into NETCONF client.

**[IMPROVEMENTS]**

-   Removed unused Karaf features.
-   Improved error message from parsing of NETCONF RPC response.
-   Removed akka actor dependency from NetconfCacheLoader.
-   Enable md-sal persistence accross sessions in NETCONF testtool.

**[FIXES]**

-   Fixed writing of netconf namespace prefix ('Namespace
    <urn:ietf:params:xml:ns:netconf:base:1.0> was not bound, please fix
    the caller').
-   Fixed reading of the whole list/leaf-list from the device - it was
    reading the whole parent structure, not only the dedicated list.
-   Moving state to unable-to-connect after failed schema context
    building from device YANGs.

- Switched order in which mountpoint and OPER data is created - mountpoint should be created before OPER data with connected
:   status is written to datastore, because mount-node RPC relies on
    OPER information only.

-   Fixed deadlock that may occur on removal of Unified MP.

RESTCONF
--------

**[NEW FEATURES]**

-   Added support for RESTCONF PATCH method that includes tags.
-   Integration of UniConfig dedicated/shared transaction to RESTCONF -
    cookie with transaction-id property, create-transaction RPC, and
    close-transaction RPC.
-   Introduction of jsonb-filter query parameter used for filtering of
    data committed to database.

**[IMPROVEMENTS]**

-   Removed unused Karaf features.
-   Using RFC8040 format for errors thrown from the transaction system.

**[FIXES]**

-   Fixed RESTCONF response/request logging.
-   Fixed reading of all available RPC operations.
-   Fixed NPE that is caused by Subject.getPrincipal() - extraction of
    authentication data from AAA.
-   Fixed serialization of ordered leaf list with attributes.
-   Fixed connection leak - read-only transaction was not always closed.
-   Fixed parsing of elements without module name: If there are some
    conflicts between children elements - multiple elements with the
    same name but in different modules exist - then we should return a
    proper error message.
-   Fixed use of fields query parameters with uniconfig-native nodes.

NETCONF translation units
-------------------------

**[IMPROVEMENTS]**

-   Removed unused Karaf features.

**[FIXES]**

-   Fixed writer dependency in XR623 ISIS translation unit.
-   Ignored 'ios-xr lacp period 200' command - only 'lacp period short'
    is supported.

CLI translation units
---------------------

**[NEW FEATURES]**

-   IOS XE: created a distinct module for IOS-XE in cli-units.
-   IOS XE: added ios-xe 15 and 17 to ios-xe module.
-   IOS XE: translation units - SNMP, LACP, privilege command,
    interfaces, l2protocol, evc, route-map, bgp and network-instance
    modules, vrf definition, fhrp version, ip commands, neighbor,
    ethernet cfm mip, negotiation auto.
-   IOS XE: additions - media-type command, port-security commands, BDI
    type recognition, ethernet cfm mip command, cft commands, commands
    for bgp, prefix-list command, fhrp delay, bfd-template,
    split-horizon group in bridge-domain, added fallOverMode for vrf
    neighbor, IPv6 prefix-lists with prefix lengths, routing-policy,
    ipv6 vrrp, added synchronization and moved default-information in
    BGP, table-map, ip community-list command, redistribute command, bgp
    and interface commands, ipv6 commands, rewrite command, snmp trap,
    support for multiple l2protocols,.
-   SAOS6: translation units: Ingress ACL.
-   SASO6: additions - commands for delete untagged attributes, unset
    description command, parsing ranges in ring protection.
-   SONiC: created init and interfaces unit.
-   Huawei: translation units - interfaces.
-   Huawei: additions - global config reader and writer for bgp,
    neighbor config reader and writer, new augmentation fields for
    global and neighbor configurations.

**[IMPROVEMENTS]**

-   Removed unused Karaf features.
-   Movef service-policy from IOS/interface to IOS/QoS.
-   IOS XE: make sure all 'GigabitEthernet' interfaces are treated as
    physical, don't send unnecessary commands in interface unit, only
    send storm-control commands when needed, moved service instances and
    encapsulation in service instance in ios-xe/interface, edit readers
    and writers for bridge-domain, edited LLDP to not parse when default
    is set, speed up mounting
-   SAOS6: use the same template for service as for profile schedulers.
-   SAOS6/8: added quotes into description.

**[FIXES]**

-   IOS XE: fixed writing interface config, fixed unwanted
    lldp/cdp/switchport vlan commands commands, fixed IPv6 config writer
    template, fixed mounting of IOS XE (configuration metadata), fixed
    bridge-domain regex, fixed reading VLANs, fixed storm-control regex,
    fixed NPE in GlobalAfiSafiConfigWriter, fixed BgpAfiSafiChecks,
    fixed CommunityListConfigReader and L3VrfReader, fixed
    IndexOutOfBoundsException in BgpActionsConfigReader.
-   IOS-XR: delete methods should always be readBefore, fixed calling
    get on a null value, fixed delete of mpls-te.
-   SAOS6: fixed virtual-circuit ethernet delete, fixed reading Virtual
    Ring data, fixed reading the range of vlans in virtual ring
    commands, reading default interface.

Openconfig
----------

-   removed unused Karaf features from openconfig
-   fixed Openconfig bug with nested augmentations (fixed resolving
    augmentations path)
-   frinx-cisco-if-extension: added negotiation auto, added support for
    multiple l2protocols, added support for rewert commands, vrf
    forwarding, ip commands, fixed L2protocol description, split-horizon
    group in bridge-domain, chaed bridge-domain type to string, fhrp
    delay, fixed bad order of augmentation in
    frinx-cisco-if-extension.yang, bridge-domain, added grouping for
    L2protocol for Service instance, added grouping for L2protocol for
    Service instance, move encapsulation in service instance, move
    service instances, created augmentation for service instances, cft
    cisco specific commands, added port-security,
-   frinx-bgp-extension: added bgp extension for Huawei device,
    local-as-group, route-maps in redistribute commands, BGP neighbor,
    table-map in BGP, synchronization and moved default-information in
    BGP, added bgp fall-over mode, neighbor \<ip-add\> as-override,
    default-information originate,
-   frinx-cisco-ipvsix-extension: added yang extension for global ipv6
    commands.
-   frinx-openconfig-bgp-types: extracted typedefs for community union
    type.
-   frinx-cisco-vrrp-extension: added ipv6 vrrp augmentation, added
    vrrp-group augmentation,
-   frinx-openconfig-bgp-policy-extension: added community-list type,
-   frinx-cisco-routing-policy-extension: prefix lengths in prefix-list,
    sequence-id, forwarding-action, route-map
-   frinx-openconfig-fhrp: fhrp version
-   frinx-bfd-extension: bfd-template-config
-   frinx-oam: added ethernet cfm mip
-   frinx-openconfig-lacp: added ON lacp mode
-   frinx-qos-extension: moved service-policy from IOS/interface to
    IOS/QoS
-   frinx-snmp: added snmp-view config
-   created frinx-openconfig-evc module
-   created frinx-privilege module

