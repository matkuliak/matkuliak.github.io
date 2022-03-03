# RPC calculate-diff

This RPC creates a diff between the actual UniConfig topology nodes and
the intended UniConfig topology nodes. The RPC input contains a list of
UniConfig nodes to calculate the diff. Output of the RPC contains a list
of statements representing the diff after the commit. It also matches
all input nodes. If RPC is called with empty list of target nodes, diff
is calculated for each modified node in the UniConfig transaction. If
one node failed for any reason, the RPC will fail entirely.

![RPC calculate-diff](RPC_calculate-diff-RPC_calculate_diff.svg)

## RPC Examples

### Successful Example

The RPC calculate-diff input has two target nodes and the output
contains a list of statements representing the diff.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-diff' \
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
                    "node-id": "IOSXRN",
                    "updated-data": [
                        {
                            "path": "network-topology:network-topology/topology=uniconfig/node=IOSXRN/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/interface=GigabitEthernet0%2F0%2F0%2F0/config",
                            "data-actual": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:ethernetCsmacd\",\n    \"enabled\": true,\n    \"name\": \"GigabitEthernet0/0/0/0\"\n  }\n}",
                            "data-intended": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:ethernetCsmacd\",\n    \"enabled\": false,\n    \"name\": \"GigabitEthernet0/0/0/0dfhdfghd\"\n  }\n}"
                        }
                    ],
                    "status": "complete",
                },
                {
                    "node-id": "IOSXR",
                    "updated-data": [
                        {
                            "path": "network-topology:network-topology/topology=uniconfig/node=IOSXR/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/interface=GigabitEthernet0%2F0%2F0%2F0/config",
                            "data-actual": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:ethernetCsmacd\",\n    \"enabled\": false,\n    \"name\": \"GigabitEthernet0/0/0/0\"\n  }\n}",
                            "data-intended": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:ethernetCsmacd\",\n    \"enabled\": false,\n    \"name\": \"GigabitEthernet0/0/0/0dfhdfghd\"\n  }\n}"
                        }
                    ],
                    "status": "complete"
                }
            ]
        }
    }
}
```

### Successful Example

If the RPC input does not contain the target nodes, all touched nodes
will be invoked.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-diff' \
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
                    "node-id": "IOSXRN",
                    "deleted-data": [
                        {
                            "path": "/network-topology:network-topology/topology=uniconfig/node=xr5/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/interface=GigabitEthernet0%2F0%2F0%2F0/config",
                            "data": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:ethernetCsmacd\",\n    \"enabled\": false,\n    \"name\": \"GigabitEthernet0/0/0/0\"\n  }\n}"
                        }
                    ],
                    "status": "complete"
                },
                {
                    "node-id": "IOSXR",
                    "deleted-data": [
                        {
                            "path": "/network-topology:network-topology/topology=uniconfig/node=xr6/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/interface=GigabitEthernet0%2F0%2F0%2F0/config",
                            "data": "{\n  \"frinx-openconfig-interfaces:config\": {\n    \"type\": \"iana-if-type:ethernetCsmacd\",\n    \"enabled\": false,\n    \"name\": \"GigabitEthernet0/0/0/0\"\n  }\n}"
                        }
                    ],
                    "status": "complete"
                },
                {
                    "node-id": "XR5",
                    "reordered-lists": [
                        "path": "/network-topology:network-topology/topology=uniconfig/node=R6/frinx-uniconfig-topology:configuration/routing-policy:routing-policy/policy-definitions/policy-definition=route_policy_1/statements/statement",
                        "actual-list-keys": "[\"name=1\", \"name=3\", \"name=2\"]",
                        "intended-list-keys": "[\"name=1\", \"name=2\", \"name=3\"]"
                    ],
                    "status": "complete"
                }
            ]
        },
        "overall-status": "complete"
    }
}
```

### Failed Example

The RPC calculate-diff input has two target nodes. One of which has not
been mounted yet (AAA), the output describes the result of the
checked-commit.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-diff' \
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
                    "node-id": "AAA",
                    "status": "fail",
                    "error-type": "uniconfig-error",
                    "error-message": "Node is missing in uniconfig topology CONFIG and OPERATIONAL datastore.",
                }
                {
                    "node-id": "IOSXR",
                    "status": "fail",
                    "error-type": "uniconfig-error",
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
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-diff' \
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
