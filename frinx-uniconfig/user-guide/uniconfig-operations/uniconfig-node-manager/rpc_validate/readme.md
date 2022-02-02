# RPC validate

The external application stores the intended configuration under nodes
in the UniConfig topology. The configuration can be validated if it is
valid or not. The trigger for execution of configuration validation is
an RPC validate. RPC input contains a list of UniConfig nodes which
configuration should be validated. Output of the RPC describes the
result of the validation and matches all input nodes. It is valid to
call this RPC with empty list of target nodes - in this case, all nodes
that have been modified in the UniConfig transaction will be validated.

The configuration of nodes consists of the following phases:

1. Open transaction to device
2. Write configuration
3. Validate configuration
4. Close transaction

If one node failed in second (validation) phase for any reason, the RPC
will fail entirely.

!!!
The validation (second phase) take place only on nodes that support
this operation.
!!!

Validate RPC is shown in the figure bellow.

![RPC validate](RPC_validation-RPC_validate.svg)

## RPC Examples

### Successful Example

RPC validate input has 1 target node and the output describes the result
of the validation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:validate' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
            "target-nodes": {
                    "node": ["IOSXRN"]
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
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

### Failed Example

RPC commit input has 1 target node and the output describes the result
of the validation. Node has failed because failed validation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:validate' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
            "target-nodes": {
                    "node": ["IOSXRN"]
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
                    "status": "fail",
                    "error-message": "RemoteDevice{IOSXRN}: Validate failed. illegal reference /orgs/org[name='TESTING-PROVIDER']/traffic-identification/using-networks\n",
                    "error-type": "processing-error"
                }
            ]
        },
        "overall-status": "fail"
    }
}
```
