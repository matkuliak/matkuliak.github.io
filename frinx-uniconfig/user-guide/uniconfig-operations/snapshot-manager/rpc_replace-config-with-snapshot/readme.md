# RPC replace-config-with-snapshot

The RPC replaces the nodes in UniConfig topology in the CONFIG datastore
with selected nodes from specified snapshot. The RPC input contains the
name of the snapshot topology and the target nodes which should replace
the UniConfig nodes in the CONFIG datastore. Output of the RPC describes
the result of the operation and matches all input nodes. You cannot call
an RPC with empty target-nodes. If one node failed for any reason, RPC
will be fail entirely.

![RPC replace-config-with-snapshot](RPC_replace-config-with-snapshot-RPC_replace_config_with_snapshot.svg)

## RPC Examples

### Successful Example

RPC input contains the name of the snapshot topology which should
replace nodes from UniConfig topology in the CONFIG datastore and list
of nodes from that snapshot. RPC output contains the result of the
operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:replace-config-with-snapshot' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "name" : "snapshot1",
        "target-nodes": {
            "node": ["IOSXR","IOSXRN"]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "overall-status": "complete",
        "node-results": {
            "node-result": [
                {
                    "node-id": "IOSXR",
                    "status": "complete"
                },
                {
                    "node-id": "IOSXRN",
                    "status": "complete"
                }
            ]
        }
    }
}
```

### Failed Example

RPC input contains the name of the snapshot topology which should
replace nodes from UniConfig topology in the CONFIG datastore and list
of nodes from that snapshot. The snapshot with name (snapshot2) has not
been created yet. RPC output contains the result of the operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:replace-config-with-snapshot' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "name" : "snapshot2",
        "target-nodes": {
            "node": ["IOSXR"]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "overall-status": "fail",
        "error-message": "Snapshot with name 'snapshot2' does not exist.",
        "node-results": {
            "node-result": [
                {
                    "node-id": "IOSXR",
                    "status": "fail",
                    "error-type": "processing-error"
                }
            ]
        }
    }
}
```

### Failed Example

RPC input contains the name of the snapshot topology which should
replace nodes from UniConfig topology in the CONFIG datastore and list
of nodes from that snapshot. The snapshot name is missing in the RPC
input. The RPC output contains the result of the operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:replace-config-with-snapshot' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": ["IOSXR"]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "overall-status": "fail",
        "error-message": "Snapshot name cannot be empty.",
        "node-results": {
            "node-result": [
                {
                    "node-id": "IOSXR",
                    "status": "fail",
                    "error-type": "processing-error"
                }
            ]
        }
    }
}
```

### Failed Example

RPC input contains the name of the snapshot topology which should
replace nodes from UniConfig topology in the CONFIG datastore and list
of nodes from that snapshot. One node is missing in snapshot1 (IOSXRN).
RPC output contains the result of the operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:replace-config-with-snapshot' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "name" : "snapshot1",
        "target-nodes": {
            "node": ["IOSXR","IOSXRN"]
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
                    "node-id": "IOSXR",
                    "status": "fail",
                    "error-type": "processing-error"
                },
                {
                    "node-id": "IOSXRN",
                    "status": "fail",
                    "error-type": "processing-error",
                    "error-message": "UniConfig node does not exist in snapshot 'snapshot1'."
                }
            ]
        }
    }
}
```

### Failed Example

RPC input does not contain the target nodes, so RPC can not be executed.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:replace-config-with-snapshot' \
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
        "overall-status": "fail",
        "error-message": "Nodes are not specified in input request"
    }
}
```
