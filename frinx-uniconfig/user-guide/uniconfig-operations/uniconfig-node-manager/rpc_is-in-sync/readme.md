# RPC is-in-sync

This RPC can be used for verification whether the specified nodes are
in-sync with the current state in the Operational datastore of UniConfig
transaction. This verification is done by comparison of configuration
fingerprints. The configuration fingerprint on the device is compared
with the last configuration fingerprint saved in the Operational
datastore. A fingerprint is usually represented by a configuration
timestamp or the last transaction ID. The is-in-sync feature is
supported only for device types that have implemented translation units
for the 'frinx-configuration-metadata' OpenConfig module (using cli
units, netconf units, or uniconfig-native metadata units).

![RPC is-in-sync](RPC_is-in-sync-RPC_is_in_sync.svg)

The RPC input contains a list of UniConfig nodes for which the
verification should be completed ('target-nodes' field). Response
comprises the operation status for each of the nodes that were specified
in the RPC input. Operation status is either 'complete' with
'is-in-sync' boolean flag or 'fail', if the operation failed it is
because the specified node has not been successfully installed or
connection has been lost. Calling RPC with empty list of target nodes
will result in invocation of RPC for each node that has been modified in
the UniConfig transaction. If the operation for one of the target nodes
fails for any reason, 'overall-status' will be set to 'fail'.

Possible RPC outputs per target node:

1. 'status' field with value 'complete' with set 'is-in-sync' boolean
    flag; is-in-sync feature is supported and the configuration
    fingerprints have been successfully compared.
2. 'status' field with value 'fail' with set 'error-type' to
    'no-connection' and corresponding 'error-message'; Unified
    mountpoint doesn't exist because the connection has been lost or the
    node has not been mounted yet.
3. 'status' field with value 'fail' with set 'error-type' to
    'uniconfig-error' and corresponding 'error-message'; reading of the
    fingerprint from the Operational datastore or Unified mountpoint has
    failed, or the configuration metadata parsing is not supported for
    the device type.

Execution of the 'is-in-sync' RPC doesn't modify the Operational
datastore. The configuration fingerprint that is stored in the
Operational datastore is not updated. 'Sync-from-network' RPC must be
used for updating the last configuration fingerprint and the actual
configuration state.

## RPC Examples

### Successful Example

the RPC input contains valid nodes for which the synchronization status
must be checked ('node1' is synced while 'node2' is not synced):

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:is-in-sync' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": [
                "node1",
                "node2"
            ]
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
                    "node-id": "node1",
                    "is-in-sync": true,
                    "status": "complete"
                },
                {
                    "node-id": "node2",
                    "is-in-sync": false,
                    "status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```
### Successful Example

If the RPC input does not contain the target nodes, all touched nodes
will be invoked.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:is-in-sync' \
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
                    "node-id": "node1",
                    "is-in-sync": true,
                    "status": "complete"
                },
                {
                    "node-id": "node2",
                    "is-in-sync": false,
                    "status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}

```

### Failed Example

RPC input contains 2 invalid nodes, the 'nodeX' has not been mounted yet
and 'example2' doesn't support comparison of fingerprints (metadata
translation unit has not been implemented for this device).

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:is-in-sync' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": [
                "nodeX",
                "example2"
            ]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "overall-status": "fail",
        "node-results": {
            "node-result": [
                {
                    "node-id": "example2",
                    "status": "fail",
                    "error-message": "Unable to check configuration fingerprint - parsing of configuration fingerprint is not implemented for this device type.",
                    "error-type": "uniconfig-error"
                },
                {
                    "node-id": "nodeX",
                    "status": "fail",
                    "error-message": "Unified mountpoint not found.",
                    "error-type": "no-connection"
                }
            ]
        }
    }
}
```

### Failed Example

RPC input contains 2 nodes, the first one ('node1') is valid and synced, the second one ('nodeX') has not been mounted yet. If there is one invalid node, Uniconfig will be evaluate nodes with fail. However, 'overall-status' will be set to 'fail'.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:is-in-sync' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": [
                "node1",
                "nodeX"
            ]
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
                    "node-id": "node1",
                    "status": "fail",
                    "error-type": "processing-error"
                },
                {
                    "node-id": "nodeX",
                    "status": "fail",
                    "error-message": "Unified mountpoint not found.",
                    "error-type": "no-connection"
                }
            ]
        },
        "overall-status": "fail"
    }
}
```

### Failed Example

If the RPC input does not contain the target nodes and there weren't any
touched nodes, the request will result in an error.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:is-in-sync' \
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
        "error-message": "There aren't any nodes specified in input RPC and there aren't any touched nodes.",
        "overall-status": "fail"
    }
}
```
