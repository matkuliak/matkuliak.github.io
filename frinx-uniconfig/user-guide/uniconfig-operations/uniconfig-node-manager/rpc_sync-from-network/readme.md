# RPC sync-from-network

The purpose of this RPC is to synchronize configuration from network
devices to the UniConfig nodes in the Operational datastore of UniConfig
transaction. The RPC input contains a list of the UniConfig nodes where
the configuration should be refreshed within the network. Output of the
RPC describes the result of sync-from-network and matches all input
nodes. Calling RPC with empty list of target nodes results in syncing
configuration of all nodes that have been modified in the UniConfig
transaction. If one node failed for any reason, the RPC will fail
entirely.

![RPC sync-from-network](RPC_sync-from-network-RPC_sync_from_network.svg)

## RPC Examples

### Successful Example

RPC input contains nodes where configuration should be refreshed.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:sync-from-network' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": ["IOSXR","IOSXRN"]
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
                    "node-id": "IOSXRN",
                    "status": "complete"
                },
                {
                    "node-id": "IOSXR",
                    "status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```


RPC input does not contain the target nodes, all touched nodes will be
invoked.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:sync-from-network' \
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
        "overall-status": "complete"
    }
}
```

### Failed Example

RPC input contains a list of nodes where the configuration should be
refreshed. One node has not been mounted yet (AAA).

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:sync-from-network' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": ["IOSXR","AAA"]
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
                    "node-id": "IOSXR",
                    "status": "complete"
                },
                {
                    "node-id": "AAA",
                    "status": "fail",
                    "error-type": "no-connection",
                    "error-message": "Unified mountpoint not found."
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
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:sync-from-network' \
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
