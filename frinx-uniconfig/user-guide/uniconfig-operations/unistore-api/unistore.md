UniStore API
============

Introduction
------------

UniStores nodes are used for storing and management of various
settings/configuration inside UniConfig. The difference between UniStore
and UniConfig nodes is that UniConfig nodes are backed by a
(real/network) device whereas UniStore nodes are not reflected by any
real device. In case of UniStore nodes, UniConfig is used only for
management of the configuration and persistence of this configuration
into PostgreSQL DBMS.

Summarized characteristics of UniStore nodes:

-   UniStore nodes are not backed by 'real' devices / southbound
    mount-points - they are used only for storing some configuration -
    configuration is only committed to PostgreSQL DBMS.
-   Configuration of UniStore node can be read, created, removed, and
    updated the same way as it is done with UniConfig topology nodes -
    user can use the same set of CRUD RESTCONF operations and supported
    UniConfig RPCs for operation purposes.
-   UniStore nodes are placed in a dedicated 'unistore' topology under
    network-topology nodes. The whole configuration is placed under
    'configuration' container.
-   UniStore configuration is modelled by user-provided YANG schemas
    that can be loaded into UniConfig - at creation of UniStore node,
    user must provide name of the YANG repository, so UniConfig known
    how to parse configuration (query parameter
    'uniconfig-schema-repository').

UniConfig operations that are supported for UniStore nodes:

-   all RESTCONF CRUD operations
-   commit / checked-commit RPC
-   calculate-diff RPC (including git-like-diff flavour)
-   subtree-manager RPCs
-   replace-config-with-oper RPC
-   revert-changes RPC (transaction-log feature)

> **note**
>
> Node ID of UniStore node must be unique among all UniConfig and
> UniStore nodes.

Commit operation
----------------

Actions performed with UniStore nodes during commit operations:

1.  Configuration fingerprint verification - if another UniConfig
    transaction has already changed one of the UniStore nodes touched in
    the current transaction, then commit operation must fail.
2.  Calculation of diff operation across all changed UniStore nodes.
3.  Writing intended configuration into UniConfig transaction.
4.  Rebasing actual configuration by intended in the UniConfig
    transaction.
5.  Updating last configuration fingerprint to the UUID of committed
    transaction.
6.  Writing transaction-log into transaction.
7.  Committing UniConfig transaction - cached changes are sent to
    PostgreSQL DBMS.

Example use-case
----------------

### Preparation of YANG repository

User must feed UniConfig with YANG repository, that will be used for
modeling of UniStore node configuration. The same UniStore node can me
modeled only by 1 YANG repository, however, different nodes can track
next different YANG repositories. YANG repository can be provided to
UniConfig by copying directory with YANG files under 'cache' parent
directory. Afterwards it is loaded either at startup or in runtime using
'register-repository' RPC.

For demonstration purposes, let's assume that cache contains YANG
repository 'system' with simple YANG module:

> **YANG file**
>
> <**config@2021-09-30.yang>:\*\*

### Creation of UniStore node

The next request shows creation of new UniStore node 'global' using
provided JSON payload and name of the YANG repository that is used for
parsing of the provided payload (query parameter
'uniconfig-schema-repository'). Note that this yang repository must be
specified only at the initialization of UniStore node.

> **Creation of UniStore node with ID 'global'**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Reading content of UniStore node

The following sample shows reading of UniStore node content using
regular GET request. Query parameter 'content' is set to 'config' to
point out the fact that UniStore node is cached only in the
Configuration data-store of transaction (Operational data-store is at
this time empty).

> **Reading UnisStore node with ID 'global'**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Calculate-diff RPC (created node)

Calculate-diff operation is also supported for UniStore nodes. the
following request shows difference of all touched nodes in the current
transaction including UniStore nodes. Since UniStore node has only been
created, diff output only contains 'created-data' with whole root
'settings' container.

> **Calculate-diff RPC**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Persistence of UniStore node

In case of UniStore nodes, commit RPC is used for confirming done
changes and storing them into PostgreSQL DBMS. As it was explained in
the previous section, commit operation causes storing of UniStore node
configuration and transaction-log in the DBMS, operation doesn't touch
any network device.

> **Commit RPC**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

> **note**
>
> It is possible to combine changes of UniStore and UniConfig nodes in
> the same transaction and commit them at once.

### Reading committed configuration

The configuration is also visible in the Operation data-store of newly
created transaction since it was committed in the previous step. The
actual state can be shown by appending 'content=nonconfig' query
parameter to GET request as it is shown in the next example.

> **Reading UnisStore node with ID 'global'**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Verification of configuration fingerprint

Configuration fingerprint is used as part of the optimistic locking
mechanism - by comparison of the configuration fingerprint from the
beginning of the transaction and at commit operation it is possible to
find out if other UniConfig transaction has already changed affected
UniStore node. In case of UniStore nodes, fingerprint is always updated
to the value of transaction-id (UUID) of the last committed transaction
that contained the UniStore node.

> **Reading UnisStore node with ID 'global'**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Modification of configuration

The same RESTCONF CRUD operations that can be applied to UniConfig nodes
are also relevant within UniStore nodes. The following request
demonstrates merging of multiple fields using PATCH operation.

> **Merging configuration**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Calculate-diff RPC (updated node)

The second calculate-diff RPC shows more granular changes done into
existing UniStore node - it contains 'create-data' and 'updated-data'
entries.

> **Calculate-diff RPC**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Commit made changes

Persistence of made changes under UniStore node can be done using commit
RPC.

> **Commit RPC**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Displaying content of transaction-log

Committed transactions including all metadata (e.g serialized diff
output or transaction ID) can be displayed by reading of
'transactions-metadata' container in the Operational data-store. It also
displays information about successfully committed UniStore nodes.
Afterwards, user can leverage this information and revert some changes
using transaction-id that is shown in the transaction-log.

> **Reading entries of transaction-log**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

### Removal of UniStore node

UniStore node can be removed by sending DELETE request to whole 'node'
list entry, 'configuration' container, or by removing of all children
'configuration' entities. In all cases, UniStore node will be removed
after confirming of changes using commit RPC.

> **Removal of UniStore node**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**

> **Commit RPC**
>
> **HTTP request:**

> **Response**
>
> **HTTP response:**
