# RPC create-snapshot

RPC creates a snapshot from the nodes in UniConfig topology. Later, this
snapshot can be used for manual rollback. RPC input contains the name of
the snapshot topology and nodes that the snapshot will contain. Output
of the RPC describes the result of operation and matches all input
nodes. You cannot call an RPC with empty target-nodes. If one node
failed for any reason, RPC will be fail entirely.

![RPC create-snapshot](RPC_create-snapshot-RPC_create_snapshot.svg)

## RPC Examples

### Successful Example

RPC input contains the name for the topology snapshot and nodes that the
snapshot contains. RPC output contains the result of operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:create-snapshot' \
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

RPC input contains the name for the topology snapshot and nodes that
will be contained in the snapshot. You cannot call an RPC with empty
target-nodes. If one node failed for any reason, RPC will be fail
entirely.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:create-snapshot' \
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
        "error-message": "Cannot create snapshot. Maximum number of snapshots exceeded. Snapshot limit: 5",
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
                    "error-type": "processing-error"
                }
            ]
        }
    }
}
```

### Failed example

The RPC input includes nodes that will be contained in the snapshot, but
a snapshot name is missing. RPC output contains the result of the
operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:create-snapshot' \
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
        "node-results": {
            "node-result": [
                {
                    "node-id": "IOSXRN",
                    "error-type": "processing-error",
                    "status": "fail"
                },
                {
                    "node-id": "IOSXR",
                    "error-type": "processing-error",
                    "status": "fail"
                }
            ]
        },
        "error-message": "Cannot create snapshot: Snapshot name cannot be empty. ",
        "overall-status": "fail"
    }
}
```

### Failed Example

RPC input contains a name for the topology snapshot and a node that will
be contained in the snapshot. One has not been mounted yet. RPC output
contains the result of the operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:create-snapshot' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "name" : "snapshot1",
        "target-nodes": {
            "node": ["AAA"]
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
                    "node-id": "AAA",
                    "error-message": "Node is missing in uniconfig topology OPERATIONAL datastore.",
                    "error-type": "processing-error",
                    "status": "fail"
                }
            ]
        }
    }
}
```

### Failed Example

RPC input does not contain the target nodes, so the RPC can not be executed.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/snapshot-manager:create-snapshot' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "name" : "snapshot1",
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
