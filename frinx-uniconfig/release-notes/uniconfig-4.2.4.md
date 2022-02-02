---
order: 2
---

UniConfig 4.2.4
===============

UniConfig:
----------

-   **Added uniconfig node status** - each node is in one of these
    states: installing, installed, failed
-   **Added unified node status** - each node is in one of these states:
    installing, installed, failed
-   bugfixing

UniConfig Native
----------------

-   **UniConfig Native for CLI** - new experimental feature allowing to
    communicate with devices in a native way using hand-written YANG
    models
-   **Added sequence-read-active param** - this forces UniConfig to read
    root configuration elements sequentially.

CLI
---

-   **Introduced RPC execute-and-expect** - It is a form of the
    ‘execute-and-read’ RPC that additionally may contain ‘expect(..)’
    patterns used for waiting for specific outputs/prompts. It can be
    used for execution of interactive commands that require multiple
    subsequent inputs with different preceding prompts.
-   **Introduced Tree-parser as CLI parsing strategy** - device
    configuration is parsed into a tree. It provides faster lookup
    operations for reads.
-   **Introduced native CLI** - feature allows to define YANG models
    instead of translation units. YANG models need to be created based
    on device specific CLI commands

OpenConfig
----------

-   **added various extensions for Ciena TUs**

NETCONF
-------

-   bugfixing

Translation units
-----------------

-   Added CLI translation units for Ciena SAOS6 and SAOS8
-   bugfixing

