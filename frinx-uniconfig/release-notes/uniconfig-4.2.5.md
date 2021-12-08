UniConfig 4.2.5
===============

UniConfig:
----------

-   **new feature**: show-connection-status RPC: it can be used for
    verification of status of selected nodes on CLI, NETCONF, Unified,
    and Uniconfig layers -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/uniconfig-api/uniconfig-node-manager/rpc_show_connection_status/show_connection_status.html)
-   **new feature**: filtering of data that is read from NETCONF
    mountpoint based on YANG extension that can be placed in the mount
    request ('uniconfig-config:extension' parameter)
    <https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/network-management-protocols/uniconfig_mounting/mounting-process.html#example-mounting-of-uniconfig-native-netconf-device>
-   **new feature**: is-in-sync RPC: verification if UniConfig Operation
    datastore is in sync with device -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/uniconfig-api/uniconfig-node-manager/rpc_is-in-sync/is-in-sync.html)
-   **new feature**: introduced 'install-uniconfig-node-enabled' mount
    request parameter - option to not install node in the Unified and
    UniConfig layers - node would be installed only in the southbound
    layer -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/network-management-protocols/uniconfig_mounting/mounting-process.html#uniconfig-native)
-   **new feature**: introduced uniconfig-native translation units used
    for reading and parsing of only configuration fingerprint
-   ****improvement****: calculate diff for uniconfig-native nodes diff
    output shows difference also on the level of leaves and leaf-lists
    (better granularity)
-   fixed setting of maximum snapshot limit (passing 0 in input)
-   fixed uniconfig-native - mounting node using CLI and afterwards
    using NETCONF uniconfig-native didn't work as expected
-   fixed caching of read operational data: improved performance for
    nodes that are mounted via NETCONF translation units

CLI:
----

-   **new component**: creation of CLI flavour for SAOS devices for
    successfull reading and parsing of device configuration
-   **new component**: "one-line-parser" CLI parsing engine that uses
    grep function for parsing running-configuration
-   fixed synchronization of UniConfig operations (for example, commit
    RPC) and CLI RPCs (for example, execute-and-read)

NETCONF:
--------

-   **new feature**: added support for invocation of YANG 1.1 actions
    and TAILF actions -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/restconf.html#post-rests-data-path-to-operation)
-   **new feature**: NETCONF edit-config test option - controlling
    validation of sent edit-config messages on NETCONF server -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/network-management-protocols/uniconfig_mounting/mounting-process.html#id4)
-   **new feature**: introduced 'default' NETCONF cache repository that
    can be used for side-loading of missing/fixed YANG schemas that are
    invalid/not provided by NETCONF device -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/network-management-protocols/uniconfig_netconf/netconf-intro.html#local-netconf-default-cache-repository)
-   **new feature**: introduced logging of whole NETCONF communcation -
    per-device NETCONF messages, notifications, and system events -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/logging/logging.html)
-   **improvement**: added NETCONF cache directory (NETCONF repostory)
    into Operational datastore of NETCONF node
-   fixed authentication in NETCONF testtool (key-pair provider)
-   fixed parsing of NETCONF replies that contains multiple RPC errors
    (severity of error was not correctly considered)
-   fixed creation of NETCONF mountpoint - it was not blocking, so
    higher layers haven't caught events in the correct order
-   fixed loading of NETCONF cache repository into Operational datastore
    - synchronization issues
-   fixed propagation of user-friendly error messages from NETCONF layer
    into UniConfig RPC output

RESTCONF:
---------

-   **new feature**: subscription to NETCONF device notifications via
    websockets -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/websocket-notifications/index.html)
-   **new feature**: invocation of YANG 1.1 actions and TAILF actions -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/restconf.html#post-rests-data-path-to-operation)
-   **new feature**: invocation of PLAIN PATCH operation -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/restconf.html#patch-rests-data-identifier)
-   **new feature**: schema filtering based on YANG extensions and
    deprecated YANG statement - reading and modification of data -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/restconf.html#device-schema-filters)
-   **new feature**: introduced logging of whole RESTCONF communcation
    with option to hide fields with selected YANG type -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/logging/logging.html)
-   **improvement**: improved RESTCONF error messages in case of invalid
    URI - displaying possible children nodes
-   fixed reading of whole list under augmentation/choice node

CONTROLLER:
-----------

-   **new feature**: introduced PostgreSQL persistence system for
    UniConfig nodes: persisting node configuration and NETCONF
    repositories into DBS with recovery system in the cluster -
    [here](https://docs.frinx.io/frinx-odl-distribution/oxygen/user-guide/high-availability-cluster/index.html#clustering-with-relational-database)
-   **upgrade**: using TrieMap dependency for data-tree implementation

DISTRIBUTION:
-------------

-   added support for Java 11: compilation of all projects using JDK 11
    and also running of UniConfig distribution using JRE 11
-   fixed invocation of UniConfig with "--help" argument
-   changed logging framework from log4j to logback
-   added "--debug" parameter for opening debug session

TRANSLATION UNITS:
------------------

-   fixed invocation of subtree writers based on wildcard path

NETCONF TRANSLATION UNITS:
--------------------------

-   **XR6**: added L3VPNIPV4UNICAST afi-safi type
-   **XR6**: fixed BGP neighbor reader
-   **JUNOS17**: fixed LACP units

CLI TRANSLATION UNITS:
----------------------

-   **SAOS**: create readers and writers for logical-ring
-   **SAOS**: fixed sending of commit command, parsing of port range,
    dependencies between writers, parsing of connection point key,
    interface subport writer, registering of interface writer, hardening
    update commands, L2VSICP writer, getAllIds in PortReader
-   **IOS**: added translation units: QoS, interface statistics,
    service-policy, VLAN, routing-policy
-   **IOS**: modified translation units: added next parameters into BGP,
    switchport mode options: dot1q && access, BGP neighbor version,
    SPEED parameter, ICMP type into ACL entry
-   **IOS-XR**: fixed LACP bugs: 'mode on' configuration is now
    explicit, subinterfaces were wrongly added to list of LAG interfaces
-   **Arista**: added init unit
-   **Cubro**: added CLI flavour

OPENCONFIG:
-----------

-   **frinx-qos-extension**: added support for CoS and DSCP in QoS
-   **frinx-cisco-if-extension**: added switchport mode options: dot1q,
    access
-   **frinx-bgp-extension**: added BGP neighbor version support
-   **frinx-if-ethernet-extension**: added interface SPEED parameter
-   **frinx-cisco-if-extension**: added port-type,
    snmp-trap-link-status, switchport-mode, switchport-access-vlan,
    switchport-trunk-allowed-vlan-add, ip-redirects, ip-unreachables,
    ip-proxy-arp, service-policy
-   created SAOS model extension (frinx-saos-virtual-ring-extension)
-   created Cisco BGP model extension (frinx-cisco-bgp-extension)
-   fixed frinx-bgp-extension YANG
-   fixed auto-generated yang docs

Known Issues:
-------------

The error message needs to be fixed to inform user about the name clash
and how to fix it. ODL does not start if cache folder for SROS16 device
is applied

**BGP**: - NullPointerException occurs when configure network instances
for XE

**NETCONF**: - Junos 18 is can't be mounted by netconf - Xrv6.2.3 device
has been locked after specific set of commands

**CLI**: - Performance issues when is more than 400 devices connected
