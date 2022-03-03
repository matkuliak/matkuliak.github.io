# Dry-run manager

## RPC dryrun-commit

The RPC will resolve the diff between actual and intended configuration
of nodes by using UniConfig Node Manager. Changes for CLI nodes are
applied by using cli-dryrun mountpoint which only stores translated CLI
commands to the cli-dry-run journal. After all changes are applied, the
cli-dryrun journal is read and an RPC output is created and returned. It
works similarly with NETCONF devices, but it outputs NETCONF messages
instead of CLI commands. RPC input contains a list of UniConfig nodes
for which to execute the dry run. Output of the RPC describes the
results of the operation and matches all input nodes. It also contains a
list of commands, and NETCONF messages for the given nodes. If RPC is
called with empty list of target nodes, dryrun operation is executed on
all modified nodes in the UniConfig transaction. If one node failed for
any reason the RPC will be failed entirely.

![RPC dryrun commit](RPC_dry-run-RPC_dryrun_commit.svg)

## RPC Examples

### Successful example

RPC input contains the target node and the output contains a list of
commands which would be sent to the device if the RPC commit or
checked-commit was called.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/dryrun-manager:dryrun-commit' \
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
                    "configuration": "<rpc message-id=\"m-23\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<lock>\n<target>\n<candidate/>\n</target>\n</lock>\n</rpc>\n<rpc message-id=\"m-24\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<get-config>\n<source>\n<candidate/>\n</source>\n<filter xmlns:ns0=\"urn:ietf:params:xml:ns:netconf:base:1.0\" ns0:type=\"subtree\">\n<interface-configurations xmlns=\"http://cisco.com/ns/yang/Cisco-IOS-XR-ifmgr-cfg\"&gt;\n&lt;interface-configuration&gt;\n&lt;active&gt;act&lt;/active&gt;\n&lt;interface-name&gt;GigabitEthernet0/0/0/1&lt;/interface-name&gt;\n&lt;/interface-configuration&gt;\n&lt;/interface-configurations&gt;\n&lt;/filter&gt;\n&lt;/get-config&gt;\n&lt;/rpc&gt;\n<rpc message-id=\"m-25\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<edit-config>\n<target>\n<candidate/>\n</target>\n<config>\n<interface-configurations xmlns=\"http://cisco.com/ns/yang/Cisco-IOS-XR-ifmgr-cfg\"&gt;\n&lt;interface-configuration&gt;\n&lt;active&gt;act&lt;/active&gt;\n&lt;interface-name&gt;GigabitEthernet0/0/0/1&lt;/interface-name&gt;\n&lt;mtus/&gt;\n&lt;/interface-configuration&gt;\n&lt;/interface-configurations&gt;\n&lt;/config&gt;\n&lt;/edit-config&gt;\n&lt;/rpc&gt;\n<rpc message-id=\"m-26\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<commit/>\n</rpc>\n<rpc message-id=\"m-27\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<unlock>\n<target>\n<candidate/>\n</target>\n</unlock>\n</rpc>\n<rpc message-id=\"m-28\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<lock>\n<target>\n<candidate/>\n</target>\n</lock>\n</rpc>\n<rpc message-id=\"m-29\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<commit/>\n</rpc>\n<rpc message-id=\"m-30\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<unlock>\n<target>\n<candidate/>\n</target>\n</unlock>\n</rpc>\n",
                    "configuration-status": "complete"
                },
                {
                    "node-id": "IOSXR",
                    "configuration": "2019-09-13T09:03:34.072: configure terminal\n2019-09-13T09:03:34.073: interface GigabitEthernet0/0/0/1fghgfhfh\nno shutdown\nroot\n\n2019-09-13T09:03:34.073: commit\n2019-09-13T09:03:34.074: end\n",
                    "configuration-status": "complete"
                }
            ]
        }
    }
}
```

### Successful example

RPC input does not contain target nodes, dryrun is executed with all
modified nodes.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/dryrun-manager:dryrun-commit' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw ' {
     "output": {
         "node-results": {
             "node-result": [
                 {
                     "node-id": "IOSXRN",
                     "configuration": "<rpc message-id=\"m-23\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<lock>\n<target>\n<candidate/>\n</target>\n</lock>\n</rpc>\n<rpc message-id=\"m-24\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<get-config>\n<source>\n<candidate/>\n</source>\n<filter xmlns:ns0=\"urn:ietf:params:xml:ns:netconf:base:1.0\" ns0:type=\"subtree\">\n<interface-configurations xmlns=\"http://cisco.com/ns/yang/Cisco-IOS-XR-ifmgr-cfg\"&gt;\n&lt;interface-configuration&gt;\n&lt;active&gt;act&lt;/active&gt;\n&lt;interface-name&gt;GigabitEthernet0/0/0/1&lt;/interface-name&gt;\n&lt;/interface-configuration&gt;\n&lt;/interface-configurations&gt;\n&lt;/filter&gt;\n&lt;/get-config&gt;\n&lt;/rpc&gt;\n<rpc message-id=\"m-25\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<edit-config>\n<target>\n<candidate/>\n</target>\n<config>\n<interface-configurations xmlns=\"http://cisco.com/ns/yang/Cisco-IOS-XR-ifmgr-cfg\"&gt;\n&lt;interface-configuration&gt;\n&lt;active&gt;act&lt;/active&gt;\n&lt;interface-name&gt;GigabitEthernet0/0/0/1&lt;/interface-name&gt;\n&lt;mtus/&gt;\n&lt;/interface-configuration&gt;\n&lt;/interface-configurations&gt;\n&lt;/config&gt;\n&lt;/edit-config&gt;\n&lt;/rpc&gt;\n<rpc message-id=\"m-26\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<commit/>\n</rpc>\n<rpc message-id=\"m-27\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<unlock>\n<target>\n<candidate/>\n</target>\n</unlock>\n</rpc>\n<rpc message-id=\"m-28\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<lock>\n<target>\n<candidate/>\n</target>\n</lock>\n</rpc>\n<rpc message-id=\"m-29\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<commit/>\n</rpc>\n<rpc message-id=\"m-30\" xmlns=\"urn:ietf:params:xml:ns:netconf:base:1.0\">\n<unlock>\n<target>\n<candidate/>\n</target>\n</unlock>\n</rpc>\n",
                     "configuration-status": "complete"
                 },
                {
                    "node-id": "IOSXR",
                    "configuration": "2019-09-13T09:03:34.072: configure terminal\n2019-09-13T09:03:34.073: interface GigabitEthernet0/0/0/1fghgfhfh\nno shutdown\nroot\n\n2019-09-13T09:03:34.073: commit\n2019-09-13T09:03:34.074: end\n",
                    "configuration-status": "complete"
                }
            ]
        },
        "overall-status": "complete"
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

RPC input contains the target node and the output contains a list of
commands which would be sent to the device if the RPC commit or
checked-commit was called. One node does not support dry-run.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/dryrun-manager:dryrun-commit' \
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
        "node-results": {
            "node-result": [
                {
                    "node-id": "IOSXR",
                    "error-message": "Node does not support dry-run",
                    "error-type": "no-connection",
                    "configuration-status": "fail"
                }
            ]
        }
    }
}
```

### Failed Example

RPC input contains the target node and the output contains a list of
commands which would be sent to the device if the RPC commit or
checked-commit was called. One node has a bad configuration.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/dryrun-manager:dryrun-commit' \
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
        "overall-configuration-status": "fail",
        "node-results": {
            "node-result": [
                {
                    "node-id": "IOSXRN",
                    "rollback-status": "complete",
                    "error-message": "Supplied value \"GigabitEthernet0/0/0/1gfhjk\" does not match required pattern \"^(?:(([a-zA-Z0-9_]*\\d+/){3,4}\\d+)|(([a-zA-Z0-9_]*\\d+/){3,4}\\d+\\.\\d+)|(([a-zA-Z0-9_]*\\d+/){2}([a-zA-Z0-9_]*\\d+))|(([a-zA-Z0-9_]*\\d+/){2}([a-zA-Z0-9_]+))|([a-zA-Z0-9_-]*\\d+)|([a-zA-Z0-9_-]*\\d+\\.\\d+)|(mpls)|(dwdm))$\"\n",
                    "error-type": "uniconfig-error",
                    "configuration-status": "fail"
                },
                {
                    "node-id": "IOSXR",
                    "configuration": "2019-09-13T08:37:28.331: configure terminal\n2019-09-13T08:37:28.536: interface GigabitEthernet0/0/0/1\nshutdown\nroot\n\n2019-09-13T08:37:28.536: commit\n2019-09-13T08:37:28.536: end\n",
                    "configuration-status": "complete",
                    "rollback-status": "complete"
                }
            ]
        }
    }
}
```

### Failed Example

RPC input contains the target node and the output contains a list of
commands which would be sent to a device if the RPC commit or
checked-commit was called. One node does not support dry-run (IOSXR) and
one is not in the unified topology (IOSXRN). There is one extra node,
which has not been mounted yet (AAA).

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/dryrun-manager:dryrun-commit' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": ["IOSXR","IOSXRN","AAA"]
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
                            "error-message": "Node has not been mounted yet.",
                            "error-type": "no-connection",
                            "configuration-status": "fail"
                },
                {
                    "node-id": "IOSXR",
                            "error-message": "Node does not support dry-run.",
                            "error-type": "no-connection",
                            "configuration-status": "fail"
                },
                {
                    "node-id": "IOSXRN",
                            "error-message": "Unified mountpoint not found.",
                            "error-type": "no-connection",
                            "configuration-status": "fail"
                }
            ]
        }
    }
}
```

### Failed Example

RPC input contains a target node and the output contains a list of
commands which would be sent to a device if the RPC commit or
checked-commit was called. One node has not been mounted yet (AAA).

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/dryrun-manager:dryrun-commit' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "target-nodes": {
            "node": ["IOSXR","IOSXRN","AAA"]
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
                    "node-id": "IOSXRN",
                    "configuration-status": "fail"
                },
                {
                    "node-id": "AAA",
                    "error-message": "Node has not been mounted yet.",
                    "error-type": "no-connection",
                    "configuration-status": "fail"
                },
                {
                    "node-id": "IOSXRN",
                    "configuration-status": "fail"
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
curl --location --request POST 'http://localhost:8181/rests/operations/dryrun-manager:dryrun-commit' \
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
