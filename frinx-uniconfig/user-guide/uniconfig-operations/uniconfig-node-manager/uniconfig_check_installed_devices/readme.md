# RPC check-installed-nodes

This RPC checks if the devices given in the input are installed or not.
It checks for the database content of every device and if there is some,
then the device is installed.

## RPC Examples

### Successful example

RPC input contains a device while no devices are installed.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:check-installed-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": ["R1"]
        }
    }
}'
```

```json RPC Response, Status: 200
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:check-installed-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": ["R1"]
        }
    }
}'
```

### Successful example

RPC input contains devices (R1 and R2) and device R1 is installed.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:check-installed-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": ["R1", "R2"]
        }
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "nodes": [
            "R1"
        ]
    }
}
```

### Failed Example

RPC input doesn't specify any nodes.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:check-installed-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
        }
    }
}'
```

```json RPC Response, Status: 400
{
    "errors": {
        "error": [
            {
                "error-message": "Target nodes cannot be empty!",
                "error-tag": "missing-element",
                "error-type": "application"
            }
        ]
    }
}
```
