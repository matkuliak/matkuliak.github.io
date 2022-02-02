# RPC replace-config-with-operational

RPC replaces the UniConfig topology nodes in the Config datastore with
UniConfig topology nodes from the Operational datastore. The RPC input
contains a list of the UniConfig nodes to replace from the Operational
to the Config datastore of the UniConfig transaction. Output of the RPC
describes the result of the operation and matches all input nodes. If
RPC is invoked with empty list of target nodes, operation will be
invoked for all nodes modified in the UniConfig transaction. If one node
failed for any reason, RPC will fail entirely.

![RPC replace-config-with-operational](RPC_replace-config-with-operational-RPC_replace_config_with_operational.svg)

## RPC Examples

### Successful Example

RPC replace-config-with-operational input has 2 target nodes and the RPC
output contains the result of the operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:replace-config-with-operational' \
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

### Successful Example

The RPC input does not contain the target nodes, all touched nodes will
be invoked.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:replace-config-with-operational' \
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
                    "node-id": "IOSXR",
                    "status": "complete"
                },
                {
                    "node-id": "IOSXRN",
                    "status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

### Failed Example

RPC input contains a list of the target nodes. One node has not been
mounted yet (AAA). The RPC output contains the result of the operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:replace-config-with-operational' \
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
        "overall-status": "fail",
        "node-results": {
            "node-result": [
                {
                    "node-id": "IOSXR",
                    "status": "fail"
                },
                {
                    "node-id": "AAA",
                    "status": "fail",
                    "error-type": "processing-error",
                    "error-message": "Node is missing in uniconfig topology OPERATIONAL datastore."
                }
            ]
        }
    }
}
```

### Failed Example

If the RPC input does not contain the target nodes and there weren't any
touched nodes, the request will result in an error.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:replace-config-with-operational' \
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
