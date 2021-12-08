UniConfig 4.2.3
===============

UniConfig:
----------

-   **create Lighty based distribution** - removal of Apache Karaf
    altogether, this distribution is based on lighty.io
-   **RPC input/output rework**
-   **Unification of RPC inputs/outputs**
-   **Prevent any network wide operations if no node id has been
    passed**- All RPCs MUST specify node-id of nodes they are affecting
-   **new UniConfig transactions** - create-transaction,
    cancel-transaction are used in HA deployments
-   bugfixing

UniConfig Native
----------------

-   **separate schema contexts based on device type** - it allows to
    mount devices with same YANG models but different revisions

Lighty
------

-   **adding of AAA support**
-   **adding of TLS support**

RESTCONF
--------

-   **update to RFC-8040 based RESTCONF** - only this version runs by
    default
-   **usage of schema context based on device type for data parsing**
-   **creation of custom UniConfig JSON/XML parsers/serializers**

OpenConfig
----------

-   **added models: ipsec, frinx-if-ethernet-extension**
-   **added various extensions for Brocade TUs**

NETCONF
-------

-   **run-time loading of netconf cache repositories**
-   **division of netconf cache based on device type**
-   **creation of schema context from netconf-cache**
-   bugfixing

Translation Units
-----------------

-   bugfixing

Known Issues
------------

JSON response for GET snapshots of UniConfig-native nodes contain
generated prefix "uniconfig-\<number\>-" (e.g.
native-529687306-Cisco-IOS-XR-ifmgr-cfg:interface-configurations). This
issue does not have an impact on RPC replace-config-with-snapshot.
