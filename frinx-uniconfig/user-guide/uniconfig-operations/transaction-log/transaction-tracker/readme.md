# Transaction tracker

## Introduction

The transaction tracker is responsible for saving a transaction-metadata
to the operational snapshot after successfully executed
commit/checked-commit RPC. The transaction-metadata contains information
about performed transactions, such as:

- **transaction-id** - Identifier of transaction.
- **username** - The name of the user who made changes.
- **commit-time** - Timestamp of changes. If multiple devices are
    configured, then the 'commit-time' will contains the timestamp of
    the last update on the last device.
- **metadata** - Items in this field represent nodes that have been
    configured in the one transaction. Each item contains a diff item
    with additional information.

- **diff** - Items in this field are a specific changes. Each item 
    contains path to changes, data before change and data after change.

!!!
Data-before is visible only if data was updated or deleted.
Data-after is visible only if data was updated or created.
!!!

![transaction-tracker](transaction-tracker.png)]

## Configuration

The UniConfig stores transaction metadata only if the
'lighty-uniconfig-config.json' file contains a "maxStoredTransactions"
parameter in "transactions" container and its value is greater then 0.
It is necessary to make this setting before running UniConfig, otherwise
parameter "maxStoredTransactions" will be '0' (default value) and
transaction-log will be disabled.

```json
{
    "transactions": {
        "maxStoredTransactions": 5,
        "maxTransactionAge": 0,
        "cleaningInterval": 0,
        "uniconfigTransactionEnabled": false
    }
}
```

### Show transaction-metadata

The response to this GET request contains all stored
transaction-metadata, their IDs and other items such as node-id, updated
data before update and after update, etc.

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/transaction-log:transactions-metadata' \
--header 'Accept: application/json'
```

```json RPC Response, Status: 200
{
    {
        "transactions-metadata":{
            "transaction-metadata":[
                {
                    "transaction-id":"221aa4a5-e32e-46fd-921a-83314b190e89",
                    "username":"admin",
                    "metadata":[
                        {
                            "node-id":"xr6",
                            "diff":[
                                {
                                    "path":"/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=act,Bundle-Ether1/description",
                                    "data-after":"{\n  \"Cisco-IOS-XR-ifmgr-cfg:description\": \"bundle-ether1-description-create\"\n}"
                                },
                                {
                                    "path":"/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=act,Bundle-Ether2/description",
                                    "data-before":"{\n  \"Cisco-IOS-XR-ifmgr-cfg:description\": \"bundle-ether2-description-before\"\n}""data-after":"{\n  \"Cisco-IOS-XR-ifmgr-cfg:description\": \"bundle-ether2-description-after\"\n}"
                                },
                                
                            ]
                        }
                    ],
                    "commit-time":"2021-Mar-09 10:53:59.102 +0100"
                },
                "transaction-id":"869df9d6-9025-4849-b30b-9db4d8fb26ec",
                "username":"admin",
                "metadata":[
                    {
                        "node-id":"xr5",
                        "diff":[
                            {
                                "path":"/frinx-openconfig-interfaces:interfaces/interface=Loopback123/config",
                                "data-before":"{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:softwareLoopback\",\n    \"enabled\": true,\n    \"name\": \"Loopback123\"\n  }\n}",
                                "data-after":"{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:softwareLoopback\",\n    \"enabled\": true,\n    \"description\": \"test-description\",\n    \"name\": \"Loopback123\"\n  }\n}"
                            }
                        ]
                    }
                ],
                "commit-time":"2021-Mar-09 11:06:58.000 +0100"
            }
        ]
    }
}
```
