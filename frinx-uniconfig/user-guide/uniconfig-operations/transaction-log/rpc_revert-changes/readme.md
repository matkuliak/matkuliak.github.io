# RPC revert-changes

This RPC revert changes that were configured within one transaction. If
a user wants to revert single transaction or multiple transactions, he
must find out transaction-ids and paste them into the body of RPC. The
transaction-id is part of the transaction-metadata, that is created by a
transaction tracker after commit/checked-commit RPC.

!!!
RPC revert-changes updates data only in the CONFIGURATION Snapshot. If
we want to write reverted data to the device, we must use RPC commit
after RPC revert-changes.
!!!

![RPC revert-changes](RPC_revert-changes.png)

## Ignore non-existent nodes

If a user wants to revert multiple transactions, some transactions
metadata may contain nodes that do not currently exist in UniConfig. In
this case, the RPC fails. The user has a choice of two options:

1.  remove transaction that contain non-existent nodes from the request
    body
2.  add 'ignore-non-existing-nodes' parameter to the RPC request body
    with a value of 'true' (default: 'false')

!!!
If the user does not use the 'ignore-non-existing-nodes' parameter,
the default value 'false' is automatically used.
!!!

## RPC Examples

### Successful examples

Before reverting a transaction we need to know its ID. We will use the
GET request to display all stored transaction-metadata.

```bash RPC Request
curl --location --request GET 'http://192.168.56.11:8181/rests/data/transaction-log:transactions-metadata' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    "transactions-metadata": {
        "transaction-metadata": [
            {
                "transaction-id": "221aa4a5-e32e-46fd-921a-83314b190e89",
                "username": "admin",
                "metadata": [
                    {
                        "node-id": "xr6",
                        "diff": [
                            {
                                "path": "/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=act,Bundle-Ether1/description",
                                "data-after": "{\n  \"Cisco-IOS-XR-ifmgr-cfg:description\": \"bundle-ether1-description-create\"\n}"
                            },
                            {
                                "path": "/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=act,Bundle-Ether2/description",
                                "data-before": "{\n  \"Cisco-IOS-XR-ifmgr-cfg:description\": \"bundle-ether2-description-before\"\n}"
                                                    "data-after": "{\n  \"Cisco-IOS-XR-ifmgr-cfg:description\": \"bundle-ether2-description-after\"\n}"
                            },
                        ],
                        "topology": "uniconfig"
                    }
                ],
                "commit-time": "2021-Mar-09 10:53:59.102 +0100"
            },
            "transaction-id": "869df9d6-9025-4849-b30b-9db4d8fb26ec",
            "username": "admin",
            "metadata": [
                {
                    "node-id": "xr5",
                    "diff": [
                        {
                            "path": "/frinx-openconfig-interfaces:interfaces/interface=Loopback123/config",
                            "data-before": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:softwareLoopback\",\n    \"enabled\": true,\n    \"name\": \"Loopback123\"\n  }\n}",
                            "data-after": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:softwareLoopback\",\n    \"enabled\": true,\n    \"description\": \"test-description\",\n    \"name\": \"Loopback123\"\n  }\n}"
                        }
                    ],
                    "topology": "uniconfig"
                }
            ],
            "commit-time": "2021-Mar-09 11:06:58.000 +0100"
        },
        {
            "transaction-id": "2c4c1eb5-185a-4204-8021-2ea05ba2c2c1",
            "username": "admin",
            "metadata": [
                {
                    "node-id": "R1",
                    "diff": [
                        {
                            "path": "/frinx-openconfig-interfaces:interfaces/interface=Bundle-Ether1",
                            "data-after": "{\n  \"interface\": [\n    {\n      \"name\": \"Bundle-Ether1\",\n      \"config\": {\n        \"type\": \"iana-if-type:ieee8023adLag\",\n        \"enabled\": false,\n        \"name\": \"Bundle-Ether1\"\n      }\n    }\n  ]\n}"
                        }
                    ],
                    "topology": "uniconfig"
                },
                {
                    "node-id": "xr5",
                    "diff": [
                        {
                            "path": "/frinx-openconfig-interfaces:interfaces/interface=Loopback1/config",
                            "data-before": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:softwareLoopback\",\n    \"enabled\": true,\n    \"name\": \"Loopback1\"\n  }\n}",
                        }
                    ],
                    "topology": "uniconfig"
                }
            ],
            "commit-time": "2021-Mar-09 11:10:54.104 +0100"
        }
    ]
}
```

Reverting changes of the single transaction.

```bash RPC Request
curl --location --request POST 'http://192.168.56.11:8181/rests/operations/transaction-log:revert-changes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-transactions": {
            "transaction": [
                "221aa4a5-e32e-46fd-921a-83314b190e89"
            ]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "revert-output": {
            "result": [
                {
                    "transaction-id": "221aa4a5-e32e-46fd-921a-83314b190e89",
                    "status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

Reverting changes of multiple transactions.

```bash RPC Request
curl --location --request POST 'http://192.168.56.11:8181/rests/operations/transaction-log:revert-changes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-transactions": {
            "transaction": [
                "221aa4a5-e32e-46fd-921a-83314b190e89",
                "869df9d6-9025-4849-b30b-9db4d8fb26ec"
            ]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "revert-output": {
            "result": [
                {
                    "transaction-id": "221aa4a5-e32e-46fd-921a-83314b190e89",
                    "status": "complete"
                },
                {
                    "transaction-id": "869df9d6-9025-4849-b30b-9db4d8fb26ec",
                    "status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

Reverting changes of multiple transactions, where the transaction with
id '2c4c1eb5-185a-4204-8021-2ea05ba2c2c1' contains non-existent node
'R1'. In this case 'ignore-non-existing-nodes' with a value of 'true' is
used, and therefore the RPC will be successful.

```bash RPC Request
curl --location --request POST 'http://192.168.56.11:8181/rests/operations/transaction-log:revert-changes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "ignore-non-existing-nodes": true,
        "target-transactions": {
            "transaction": [
                "221aa4a5-e32e-46fd-921a-83314b190e89",
                "2c4c1eb5-185a-4204-8021-2ea05ba2c2c1"
            ]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "revert-output": {
            "result": [
                {
                    "transaction-id": "221aa4a5-e32e-46fd-921a-83314b190e89",
                    "status": "complete"
                },
                {
                    "transaction-id": "2c4c1eb5-185a-4204-8021-2ea05ba2c2c1",
                    "status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

### Failed example

This is a case when revert-changes request contains a non-existent
transaction in the request body.

```bash RPC Request
curl --location --request POST 'http://192.168.56.11:8181/rests/operations/transaction-log:revert-changes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-transactions": {
            "transaction": [
                "82b4e916-e1ed-4a54-97bc-067699842af6"
            ]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "revert-output": {
            "result": [
                {
                    "transaction-id": "82b4e916-e1ed-4a54-97bc-067699842af6",
                    "status": "fail",
                    "error-message": "Failed to find transaction in log with ID: 82b4e916-e1ed-4a54-97bc-067699842af6",
                    "error-type": "uniconfig-error"
                }
            ]
        },
        "overall-status": "fail"
    }
}
```

Reverting changes of multiple transactions, where the transaction
metadata with id '2c4c1eb5-185a-4204-8021-2ea05ba2c2c1' contains
non-existent node. In this case 'ignore-non-existing-nodes' with a value
of 'false' is used, and therefore the RPC fails.

```bash RPC Request
curl --location --request POST 'http://192.168.56.11:8181/rests/operations/transaction-log:revert-changes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "ignore-non-existing-nodes": false,
        "target-transactions": {
            "transaction": [
                "221aa4a5-e32e-46fd-921a-83314b190e89",
                "2c4c1eb5-185a-4204-8021-2ea05ba2c2c1"
            ]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "revert-output": {
            "result": [
                {
                    "transaction-id": "221aa4a5-e32e-46fd-921a-83314b190e89",
                    "status": "fail",
                    "error-type": "uniconfig-error"
                },
                {
                    "transaction-id": "2c4c1eb5-185a-4204-8021-2ea05ba2c2c1",
                    "status": "fail",
                    "error-message": "Transactions metadata 2c4c1eb5-185a-4204-8021-2ea05ba2c2c1 contain non-existent uniconfig nodes: [R1]",
                    "error-type": "uniconfig-error"
                }
            ]
        },
        "overall-status": "fail"
    }
}
```
