# RPC calculate-subtree-git-like-diff

This RPC creates diff between actual topology subtrees and intended
topology subtrees. Subtrees can be specified under different nodes, it
only compares data hierarchy and values. RPC input contains paths
('source-path' and 'target-path') and data location ('source-datastore'
and 'target-datastore'). Data location is enumeration with two possible
values 'OPERATIONAL' or 'CONFIGURATION'. The output of the RPC is a
difference between two subtrees which are is in a git-like style.

![RPC calculate-subtree-git-like-diff](RPC_calculate-subtree-git-like-diff-RPC_calculate_subtree_git_like_diff.svg)

## RPC Examples

### Successful example

RPC calculate-subtree-git-like-diff input has path to two interfaces
that are on different nodes. Both data locations are placed in the
CONFIGURATION datastore. Output contains a list of all the changes on
different paths. Multiple changes that occur under the same root element
are merged together. Every change has at least a source or a target
path, or both if some data are updated on that path.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-subtree-git-like-diff' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input" : {
        "source-path": ["/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=XR5/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/frinx-openconfig-interfaces:interface=MgmtEth0%2F0%2FCPU0%2F0"],
        "target-path": ["/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=XR6/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/frinx-openconfig-interfaces:interface=MgmtEth0%2F0%2FCPU0%2F0"],
        "source-datastore": "CONFIGURATION",
        "target-datastore": "CONFIGURATION"
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "status": "complete",
        "changes": [
            {
                "status": "complete",
                "target-path": "/network-topology:network-topology/topology=uniconfig/node=XR6/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces",
                "source-path": "/network-topology:network-topology/topology=uniconfig/node=XR5/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces",
                "data": "  {\n    \"frinx-openconfig-interfaces:interfaces\": {\n      \"interface\": [\n        {\n          \"key\":\"MgmtEth0/0/CPU0/0\",\n          \"subinterfaces\": {\n            \"subinterface\": [\n              {\n                \"key\":\"0\",\n                \"frinx-openconfig-if-ip:ipv4\": {\n                  \"addresses\": {\n                    \"address\": [\n-                     {\n-                       \"ip\":\"192.168.1.212\",\n-                       \"config\": {\n-                         \"prefix-length\":\"24\",\n-                         \"ip\":\"192.168.1.212\"\n-                       }\n-                     },\n+                     {\n+                       \"ip\":\"192.168.1.214\",\n+                       \"config\": {\n+                         \"prefix-length\":\"27\",\n+                         \"ip\":\"192.168.1.214\"\n+                       }\n+                     }\n                    ]\n                  }\n                },\n                \"config\": {\n                  \"enabled\": {\n                    \"actual\": {\n                      \"frinx-openconfig-interfaces:enabled\":\"false\"\n                    },\n                    \"intended\": {\n                      \"frinx-openconfig-interfaces:enabled\":\"true\"\n                    }\n                  },\n-                 \"frinx-openconfig-interfaces:index\":\"15\",\n+                 \"frinx-openconfig-interfaces:index\":\"0\"\n                }\n              }\n            ]\n          },\n          \"config\": {\n-           \"frinx-openconfig-interfaces:enabled\":\"false\",\n+           \"frinx-openconfig-interfaces:enabled\":\"true\"\n          }\n        }\n      ]\n    }\n  }\n"
            }
        ]
    }
}
```

### Successful example

The following output demonstrates a situation, when there are no changes
on different subtrees.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-subtree-git-like-diff' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input" : {
        "source-path": ["/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=XR5/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/frinx-openconfig-interfaces:interface=GigabitEthernet0%2F0%2F0%2F0"],
        "target-path": ["/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=XR6/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/frinx-openconfig-interfaces:interface=GigabitEthernet0%2F0%2F0%2F0"],
        "source-datastore": "CONFIGURATION",
        "target-datastore": "CONFIGURATION"
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "status": "complete"
    }
}
```

### Failed example

RPC input does not contain target node YIID, so RPC can not be executed.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-subtree-git-like-diff' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input" : {
        "source-path": ["/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=R1/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/frinx-openconfig-interfaces:interface=GigabitEthernet0%2F0%2F0%2F0"],
        "source-datastore": "CONFIGURATION",
        "target-datastore": "CONFIGURATION"
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "status": "fail",
        "error-message": "target-path is not specified in input request",
        "error-type": "uniconfig-error"
    }
}
```

### Failed example

RPC input does not contain target datastore type, so RPC can not be
executed.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:calculate-subtree-git-like-diff' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input" : {
        "source-path": ["/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=R1/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/frinx-openconfig-interfaces:interface=GigabitEthernet0%2F0%2F0%2F0"],
        "target-path": ["/network-topology:network-topology/network-topology:topology=uniconfig/network-topology:node=R2/frinx-uniconfig-topology:configuration/frinx-openconfig-interfaces:interfaces/frinx-openconfig-interfaces:interface=GigabitEthernet0%2F0%2F0%2F0"],
        "source-datastore": "CONFIGURATION"
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "status": "fail",
        "error-message": "target-datastore is not specified in input request",
        "error-type": "uniconfig-error"
    }
}
```
