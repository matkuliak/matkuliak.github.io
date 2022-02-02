# RPC uninstall-multiple-nodes

This RPC uninstalls multiple devices at once. It uses the default
uninstall-node RPC. Devices are uninstalled in parallel.

## RPC Examples

### Successful example

RPC input contains two devices (R1 and R2).

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:uninstall-multiple-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "nodes": [
            {
                "node-id": "R2",
                "connection-type": "cli"
            },
            {
                "node-id": "R1",
                "connection-type": "cli"
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": [
            {
                "status": "complete",
                "node-id": "R2"
            },
            {
                "status": "complete",
                "node-id": "R1"
            }
        ]
    }
}
```

### Successful example

RPC input contains devices (R1 and R2) and R2 is installed on two
different protocols.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:uninstall-multiple-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "nodes": [
            {
                "node-id": "R2",
                "connection-type": "cli"
            },
            {
                "node-id": "R2",
                "connection-type": "netconf"
            },
            {
                "node-id": "R1",
                "connection-type": "cli"
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": [
            {
                "status": "complete",
                "node-id": "R2"
            },
            {
                "status": "complete",
                "node-id": "R2"
            },
            {
                "status": "complete",
                "node-id": "R1"
            }
        ]
    }
}
```

### Successful example

RPC input contains two devices (R1 and R2) and R2 is already uninstalled
on CLI protocol.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:uninstall-multiple-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "nodes": [
            {
                "node-id": "R2",
                "connection-type": "cli"
            }
            {
                "node-id": "R1",
                "connection-type": "cli"
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": [
            {
                "status": "fail",
                "error-message": "Node hasn't been installed on Uniconfig layer",
                "node-id": "R2"
            },
            {
                "status": "complete",
                "node-id": "R1"
            }
        ]
    }
}
```

### Failed Example

RPC input doesn't specify node-id.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:uninstall-multiple-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "nodes": [
            {
                "connection-type": "cli"
            },
            {
                "node-id": "R1",
                "connection-type": "cli"
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "node-results": [
            {
                "status": "fail",
                "error-message": "Field 'node-id' must be specified in the RPC input"
            }
        ]
    }
}
```
