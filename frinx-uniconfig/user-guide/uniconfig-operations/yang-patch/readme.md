# YANG Patch Operations

Yang Patch is used for modification of subtrees under configuration. Advantages of YANG Patch in comparison
to other RESTCONF operations:
* YANG Patch may contain multiple edits with different operations applied to different subtrees
* all edits inside YANG Patch are applied atomically - either all edits are successful or PATCH operation will fail
  and configuration will not be modified
* supported reordering of lists (move operation) and inserting of list entry to specific position in the list
  (insert operation)

UniConfig supports all RFC-specified operations inside edits:

* CREATE
* REPLACE
* MERGE
* MOVE
* INSERT
* DELETE
* REMOVE
* RENAME

Using these operations, the user is able to reorder lists, create new data, remove data, or update specific data.

!!!
For more information, please refer to the official documentation of the [RFC YANG patch](https://datatracker.ietf.org/doc/html/rfc8072)
!!!

## RPC Examples

### Creation of list entries

The request creates new list entries in the tvi list.
If the data exist, return an error.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch": {
        "patch-id": "add-list-entries",
        "comment": "Adding list entries",
        "edit": [
            {
                "edit-id": "1",
                "operation": "create",
                "target": "/tvi",
                "value": {
                    "tvi": [
                        {
                            "mode": "ipsec",
                            "enable": false,
                            "name": "tvi-0/99",
                            "description": "test interface",
                            "type": "p2mp-vxlan"
                        }
                    ]
                }
            },
            {
                "edit-id": "2",
                "operation": "create",
                "target": "/tvi",
                "value": {
                    "tvi": [
                        {
                            "mode": "ipsec",
                            "enable": false,
                            "name": "tvi-0/98",
                            "description": "test interface",
                            "type": "p2mp-vxlan"
                        }
                    ]
                }
            },
            {
                "edit-id": "3",
                "operation": "create",
                "target": "/tvi",
                "value": {
                    "tvi": [
                        {
                            "mode": "ipsec",
                            "enable": false,
                            "name": "tvi-0/97",
                            "description": "test interface",
                            "type": "p2mp-vxlan"
                        }
                    ]
                }
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status":{
        "patch-id":"add-list-entries",
        "ok":[
            null
        ]
    }
}
```

### Moving list entry

The request moves an existing list entry on a user defined position.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch": {
        "patch-id": "moving-list-entry",
        "comment": "Moving a list entry",
        "edit": [
            {
                "edit-id": "4",
                "operation": "move",
                "target": "/tvi=tvi-0%2F99",
                "point": "/tvi=tvi-0%2F98",
                "where": "before"
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status":{
        "patch-id":"moving-list-entry",
        "ok":[
            null
        ]
    }
}
```

### Inserting new list entry

The request inserts new list entries on a user defined position.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch":{
        "patch-id":"insert-list-entries",
        "comment":"Inserting list entries",
        "edit":[
            {
                "edit-id":"5",
                "operation":"insert",
                "target":"/tvi=tvi-0%2F50",
                "point":"/tvi=tvi-0%2F98",
                "where":"before",
                "value":{
                    "tvi":[
                        {
                            "mode":"ipsec",
                            "enable":false,
                            "name":"tvi-0/50",
                            "description":"test interface",
                            "type":"p2mp-vxlan"
                        }
                    ]
                }
            },
            {
                "edit-id":"6",
                "operation":"insert",
                "target":"/tvi=tvi-0%2F51",
                "where":"first",
                "value":{
                    "tvi":[
                        {
                            "mode":"ipsec",
                            "enable":false,
                            "name":"tvi-0/51",
                            "description":"test interface",
                            "type":"p2mp-vxlan"
                        }
                    ]
                }
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status":{
        "patch-id":"insert-list-entries",
        "ok":[
            null
        ]
    }
}
```

### Replacing list entry

The request replaces an existing value in a list entry.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch":{
        "patch-id":"replace-list-entry-value",
        "comment":"Replacing a value in list entry",
        "edit":[
            {
                "edit-id":"7",
                "operation":"replace",
                "target":"/tvi=tvi-0%2F99/enable",
                "value":{
                    "enable":true
                }
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status":{
        "patch-id":"replace-list-entry-value",
        "ok":[
            null
        ]
    }
}
```

### Merging configuration

The request merges an existing value in a list entry.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch":{
        "patch-id":"merge-value-list-entry",
        "comment":"Merge new values in list entry",
        "edit":[
            {
                "edit-id":"8",
                "operation":"merge",
                "target":"/tvi=tvi-0%2F99/unit=0/family/inet/address",
                "value":{
                    "address":[
                        {
                            "addr":"10.1.0.101/32"
                        },
                        {
                            "addr":"1.2.3.4/32"
                        }
                    ]
                }
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status":{
        "patch-id":"merge-value-list-entry",
        "ok":[
            null
        ]
    }
}
```

### Delete list entry

The request deletes a list entry. If the data is missing, returns an error.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch":{
        "patch-id":"delete-list-entry",
        "comment":"Delete a list entry",
        "edit":[
            {
                "edit-id":"9",
                "operation":"delete",
                "target":"/tvi=tvi-0%2F98"
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status":{
        "patch-id":"delete-list-entry",
        "ok":[
            null
        ]
    }
}
```

### Removing list entry

The request removes a list entry.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch": {
        "patch-id": "remove-list-entry",
        "comment": "Remove a list entry",
        "edit": [
            {
                "edit-id": "10",
                "operation": "remove",
                "target": "/tvi=tvi-0%2F50"
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status": {
        "patch-id": "remove-list-entry",
        "ok": [
            null
        ]
    }
}
```

### Renaming list entry

The request renames a list entry key.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=vnf20/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch": {
        "patch-id": "rename-list-entry",
        "comment": "Rename a list entry key",
        "edit": [
            {
                "edit-id": "11",
                "operation": "rename",
                "target": "/tvi=tvi-0%2F98",
                "point": "/tvi=tvi-0%2F101"
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status": {
        "patch-id": "remove-list-entry",
        "ok": [
            null
        ]
    }
}
```

### Failed deleting of list entry

The request to delete a list entry that is not present.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch": {
        "patch-id": "delete-list-entry",
        "comment": "Delete a list entry",
        "edit": [
            {
                "edit-id": "9",
                "operation": "delete",
                "target": "/tvi=tvi-0%2F98"
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status": {
        "patch-id": "delete-list-entry",
        "edit-status": {
            "edit": [
                {
                    "edit-id": "9",
                    "errors": {
                        "error": [
                            {
                                "error-type": "protocol",
                                "error-tag": "data-missing",
                                "error-path": "/network-topology/topology=uniconfig/node=dev1/configuration/interfaces/tvi=tvi-0%2F98",
                                "error-message": "Data does not exist"
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```

### Sending Patch request with invalid structure

The request is missing some data.

```bash PATCH request
curl --location --request PATCH 'http://localhost:8181/rests/data/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=dev1/frinx-uniconfig-topology:configuration/interfaces' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "yang-patch": {
        "comment": "insert-lists",
        "edit": [
            {
                "edit-id": "5",
                "target": "/tvi=tvi-0%2F50",
                "point": "/tvi=tvi-0%2F98",
                "where": "before",
                "value": {
                    "tvi": [
                        {
                            "mode": "ipsec",
                            "enable": false,
                            "name": "tvi-0/50",
                            "description": "test interface",
                            "type": "p2mp-vxlan"
                        }
                    ]
                }
            }
        ]
    }
}'
```

```json PATCH response
{
    "ietf-yang-patch:yang-patch-status": {
        "patch-id": null,
        "errors": {
            "error": [
                {
                    "error-type": "application",
                    "error-tag": "missing-element",
                    "error-message": "Patch-id must be set"
                }
            ]
        },
        "edit-status": {
            "edit": [
                {
                    "edit-id": "5",
                    "errors": {
                        "error": [
                            {
                                "error-type": "application",
                                "error-tag": "missing-element",
                                "error-message": "Edit operation must be specified"
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```

