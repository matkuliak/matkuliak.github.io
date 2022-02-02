# UniStore API

## Introduction

UniStores nodes are used for storing and management of various
settings/configuration inside UniConfig. The difference between UniStore
and UniConfig nodes is that UniConfig nodes are backed by a
(real/network) device whereas UniStore nodes are not reflected by any
real device. In case of UniStore nodes, UniConfig is used only for
management of the configuration and persistence of this configuration
into PostgreSQL DBMS.

Summarized characteristics of UniStore nodes:

- UniStore nodes are not backed by 'real' devices / southbound
    mount-points - they are used only for storing some configuration -
    configuration is only committed to PostgreSQL DBMS.
- Configuration of UniStore node can be read, created, removed, and
    updated the same way as it is done with UniConfig topology nodes -
    user can use the same set of CRUD RESTCONF operations and supported
    UniConfig RPCs for operation purposes.
- UniStore nodes are placed in a dedicated 'unistore' topology under
    network-topology nodes. The whole configuration is placed under
    'configuration' container.
- UniStore configuration is modelled by user-provided YANG schemas
    that can be loaded into UniConfig - at creation of UniStore node,
    user must provide name of the YANG repository, so UniConfig known
    how to parse configuration (query parameter
    'uniconfig-schema-repository').

UniConfig operations that are supported for UniStore nodes:

- all RESTCONF CRUD operations
- commit / checked-commit RPC
- calculate-diff RPC (including git-like-diff flavour)
- subtree-manager RPCs
- replace-config-with-oper RPC
- revert-changes RPC (transaction-log feature)

!!!
Node ID of UniStore node must be unique among all UniConfig and UniStore nodes.
!!!

## Commit operation

Actions performed with UniStore nodes during commit operations:

1. Configuration fingerprint verification - if another UniConfig
    transaction has already changed one of the UniStore nodes touched in
    the current transaction, then commit operation must fail.
2. Calculation of diff operation across all changed UniStore nodes.
3. Writing intended configuration into UniConfig transaction.
4. Rebasing actual configuration by intended in the UniConfig
    transaction.
5. Updating last configuration fingerprint to the UUID of committed
    transaction.
6. Writing transaction-log into transaction.
7. Committing UniConfig transaction - cached changes are sent to
    PostgreSQL DBMS.

## Example use-case

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

```yang config@2021-09-30.yang:
module config {
    yang-version 1.1;
    namespace urn:ietf:params:xml:ns:yang:test;
    prefix test;

    revision 2021-09-30 {
        description
          "Initial revision";
    }

    container settings {
        leaf update-interval {
            type uint16;
        }

        container cluster {
            leaf ping-interval {
                type uint16;
            }
            leaf idle-timeout {
                type uint16;
            }
        }

        list routing-protocols {
            key process-id;

            leaf process-id {
                type string;
            }

            leaf-list interfaces {
                type string;
            }
        }
    }
}
```

### Creation of UniStore node

The next request shows creation of new UniStore node 'global' using
provided JSON payload and name of the YANG repository that is used for
parsing of the provided payload (query parameter
'uniconfig-schema-repository'). Note that this yang repository must be
specified only at the initialization of UniStore node.

```bash Creation of UniStore node with ID ‘global’
curl --location --request PUT 'http://localhost:8181/rests/data/network-topology:network-topology/topology=unistore/node=global/configuration/settings?uniconfig-schema-repository=system' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "settings": {
        "update-interval": 5000,
        "cluster": {
            "ping-interval": 20000,
            "idle-timeout": 100
        },
        "routing-protocols": [
            {
                "process-id": "rip",
                "interfaces": [
                    "eth0.0",
                    "eth0.1"
                ]
            },
            {
                "process-id": "ospf",
                "interfaces": [
                    "eth0.1"
                ]
            }
        ]
    }
}'
```

```json RPC Response, Status: 201

```

### Reading content of UniStore node

The following sample shows reading of UniStore node content using
regular GET request. Query parameter 'content' is set to 'config' to
point out the fact that UniStore node is cached only in the
Configuration data-store of transaction (Operational data-store is at
this time empty).

```bash Reading UniStore node with ID ‘global’
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=unistore/node=global/configuration?content=config' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "frinx-uniconfig-topology:configuration": {
        "config:settings": {
            "update-interval": 5000,
            "cluster": {
                "ping-interval": 20000,
                "idle-timeout": 100
            },
            "routing-protocols": [
                {
                    "process-id": "rip",
                    "interfaces": [
                        "eth0.0",
                        "eth0.1"
                    ]
                },
                {
                    "process-id": "ospf",
                    "interfaces": [
                        "eth0.1"
                    ]
                }
            ]
        }
    }
}
```

### Calculate-diff RPC (created node)

Calculate-diff operation is also supported for UniStore nodes. the
following request shows difference of all touched nodes in the current
transaction including UniStore nodes. Since UniStore node has only been
created, diff output only contains 'created-data' with whole root
'settings' container.

```bash Calculate-diff RPC
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-diff' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": []
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": {
            "node-result": [
                {
                    "node-id": "global",
                    "status": "complete",
                    "created-data": [
                        {
                            "path": "/network-topology:network-topology/topology=unistore/node=global/frinx-uniconfig-topology:configuration/config:settings",
                            "data": "{\n  \"config:settings\": {\n    \"update-interval\": 5000,\n    \"cluster\": {\n      \"ping-interval\": 20000,\n      \"idle-timeout\": 100\n    },\n    \"routing-protocols\": [\n      {\n        \"process-id\": \"rip\",\n        \"interfaces\": [\n          \"eth0.0\",\n          \"eth0.1\"\n        ]\n      },\n      {\n        \"process-id\": \"ospf\",\n        \"interfaces\": [\n          \"eth0.1\"\n        ]\n      }\n    ]\n  }\n}"
                        }
                    ]
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

### Persistence of UniStore node

In case of UniStore nodes, commit RPC is used for confirming done
changes and storing them into PostgreSQL DBMS. As it was explained in
the previous section, commit operation causes storing of UniStore node
configuration and transaction-log in the DBMS, operation doesn't touch
any network device.

```bash Commit RPC
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:commit' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": {
            "node-result": [
                {
                    "node-id": "global",
                    "configuration-status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

!!!
It is possible to combine changes of UniStore and UniConfig nodes in
the same transaction and commit them at once.
!!!

### Reading committed configuration

The configuration is also visible in the Operation data-store of newly
created transaction since it was committed in the previous step. The
actual state can be shown by appending 'content=nonconfig' query
parameter to GET request as it is shown in the next example.

```bash Reading UniStore node with ID **'global'**
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=unistore/node=global/configuration?content=nonconfig' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "frinx-uniconfig-topology:configuration": {
        "config:settings": {
            "update-interval": 5000,
            "cluster": {
                "ping-interval": 20000,
                "idle-timeout": 100
            },
            "routing-protocols": [
                {
                    "process-id": "rip",
                    "interfaces": [
                        "eth0.0",
                        "eth0.1"
                    ]
                },
                {
                    "process-id": "ospf",
                    "interfaces": [
                        "eth0.1"
                    ]
                }
            ]
        }
    }
}
```

### Verification of configuration fingerprint

Configuration fingerprint is used as part of the optimistic locking
mechanism - by comparison of the configuration fingerprint from the
beginning of the transaction and at commit operation it is possible to
find out if other UniConfig transaction has already changed affected
UniStore node. In case of UniStore nodes, fingerprint is always updated
to the value of transaction-id (UUID) of the last committed transaction
that contained the UniStore node.

```bash Reading UniStore node with ID **'global'**
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=unistore/node=global/configuration-metadata/last-configuration-fingerprint?content=nonconfig' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "frinx-configuration-metadata:last-configuration-fingerprint": "428d1a55-5681-4a47-8a51-634a215127d7"
}
```

### Modification of configuration

The same RESTCONF CRUD operations that can be applied to UniConfig nodes
are also relevant within UniStore nodes. The following request
demonstrates merging of multiple fields using PATCH operation.

```bash Merging configuration
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/topology=unistore/node=global/configuration/settings' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "settings": {
        "cluster": {
            "ping-interval": 10000
        },
        "routing-protocols": [
            {
                "process-id": "ospf",
                "interfaces": [
                    "eth0.1",
                    "eth0.2"
                ]
            },
            {
                "process-id": "eigrp",
                "interfaces": [
                    "g1"
                ]
            }
        ]
    }
}'
```

```json RPC Response, Status: 200

```

### Calculate-diff RPC (updated node)

The second calculate-diff RPC shows more granular changes done into
existing UniStore node - it contains 'create-data' and 'updated-data'
entries.

```bash Calculate-diff RPC
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-diff' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": []
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": {
            "node-result": [
                {
                    "node-id": "global",
                    "status": "complete",
                    "created-data": [
                        {
                            "path": "/network-topology:network-topology/topology=unistore/node=global/frinx-uniconfig-topology:configuration/config:settings/routing-protocols=eigrp",
                            "data": "{\n  \"routing-protocols\": [\n    {\n      \"process-id\": \"eigrp\",\n      \"interfaces\": [\n        \"g1\"\n      ]\n    }\n  ]\n}"
                        }
                    ],
                    "updated-data": [
                        {
                            "path": "/network-topology:network-topology/topology=unistore/node=global/frinx-uniconfig-topology:configuration/config:settings/routing-protocols=ospf",
                            "data-actual": "{\n  \"config:interfaces\": [\n    \"eth0.1\"\n  ]\n}",
                            "data-intended": "{\n  \"config:interfaces\": [\n    \"eth0.1\",\n    \"eth0.2\"\n  ]\n}"
                        },
                        {
                            "path": "/network-topology:network-topology/topology=unistore/node=global/frinx-uniconfig-topology:configuration/config:settings/cluster/ping-interval",
                            "data-actual": "{\n  \"config:ping-interval\": 20000\n}",
                            "data-intended": "{\n  \"config:ping-interval\": 10000\n}"
                        }
                    ]
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

### Commit made changes

Persistence of made changes under UniStore node can be done using commit
RPC.

```bash Commit RPC
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:commit' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": {
            "node-result": [
                {
                    "node-id": "global",
                    "configuration-status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

### Displaying content of transaction-log

Committed transactions including all metadata (e.g serialized diff
output or transaction ID) can be displayed by reading of
'transactions-metadata' container in the Operational data-store. It also
displays information about successfully committed UniStore nodes.
Afterwards, user can leverage this information and revert some changes
using transaction-id that is shown in the transaction-log.

```bash Reading entries of transaction-log
curl --location --request GET 'http://localhost:8181/rests/data/transactions-metadata/transaction-metadata?content=nonconfig' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "transaction-metadata": [
        {
            "transaction-id": "428d1a55-5681-4a47-8a51-634a215127d7",
            "username": "admin",
            "metadata": [
                {
                    "node-id": "global",
                    "diff": [
                        {
                            "path": "/config:settings",
                            "data-after": "{\"config:settings\": {\"cluster\": {\"idle-timeout\": 100, \"ping-interval\": 20000}, \"update-interval\": 5000, \"routing-protocols\": [{\"interfaces\": [\"eth0.0\", \"eth0.1\"], \"process-id\": \"rip\"}, {\"interfaces\": [\"eth0.1\"], \"process-id\": \"ospf\"}]}}"
                        }
                    ],
                    "topology": "unistore"
                }
            ],
            "commit-time": "2021-Oct-10 18:52:40.942 +0200"
        },
        {
            "transaction-id": "107e38d9-fa85-4bd7-89f3-76beac55345a",
            "username": "admin",
            "metadata": [
                {
                    "node-id": "global",
                    "diff": [
                        {
                            "path": "/config:settings/routing-protocols=eigrp",
                            "data-after": "{\"routing-protocols\": [{\"interfaces\": [\"g1\"], \"process-id\": \"eigrp\"}]}"
                        },
                        {
                            "path": "/config:settings/cluster/ping-interval",
                            "data-before": "{\"config:ping-interval\": 20000}",
                            "data-after": "{\"config:ping-interval\": 10000}"
                        },
                        {
                            "path": "/config:settings/routing-protocols=ospf",
                            "data-before": "{\"config:interfaces\": [\"eth0.1\"]}",
                            "data-after": "{\"config:interfaces\": [\"eth0.1\", \"eth0.2\"]}"
                        }
                    ],
                    "topology": "unistore"
                }
            ],
            "commit-time": "2021-Oct-10 19:12:34.241 +0200"
        }
    ]
}
```

### Removal of UniStore node

UniStore node can be removed by sending DELETE request to whole 'node'
list entry, 'configuration' container, or by removing of all children
'configuration' entities. In all cases, UniStore node will be removed
after confirming of changes using commit RPC.

```bash Removal of UniStore node
curl --location --request DELETE 'http://localhost:8181/rests/data/network-topology:network-topology/topology=unistore/node=global' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 204

```

```bash Commit RPC
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:commit' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": {
            "node-result": [
                {
                    "node-id": "global",
                    "configuration-status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```
