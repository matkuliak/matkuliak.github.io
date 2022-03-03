---
label: Updating installation parameters
order: 8000
---

# Updating installation parameters

## Overview

During device installation UniConfig creates a mount-point for this device and stores it in the database. This
mount-point contains all parameters set by the user in the installation request. UniConfig supports a feature to update
mount-point parameters. It is possible to use it for both NETCONF and CLI nodes.

### Show installation parameters

Parameters of the installed devices can be displayed using a GET request on the node. It is necessary to use the right
topology. It should return the current node settings. See the following examples:

**CLI node**
```bash
curl -X GET \
  http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=cli/node=cliNode
```
Output:
```
{
    "node": [
        {
            "node-id": "cliNode",
            "node-extension:reconcile": false,
            "uniconfig-config:install-uniconfig-node-enabled": true,
            "cli-topology:host": "192.168.1.225",
            "cli-topology:transport-type": "ssh",
            "cli-topology:dry-run-journal-size": 150,
            "cli-topology:username": "test",
            "cli-topology:password": "test",
            "cli-topology:journal-size": 150,
            "cli-topology:port": 22,
            "cli-topology:device-version": "6.2.3",
            "cli-topology:device-type": "ios xr"
        }
    ]
}
```
**NETCONF node**
```bash
curl -X GET \
  http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=netconfNode
```
Output:
```
{
    "input": {
        "node-id": "netconfNode",
        "netconf": {
            "netconf-node-topology:host": "192.168.1.216",
            "netconf-node-topology:port": 830,
            "netconf-node-topology:keepalive-delay": 0,
            "netconf-node-topology:tcp-only": false,
            "netconf-node-topology:username": "test",
            "netconf-node-topology:password": "test",
            "uniconfig-config:uniconfig-native-enabled": true,
            "netconf-node-topology:edit-config-test-option": "test-then-set",
            "uniconfig-config:blacklist": {
                "uniconfig-config:path": [
                    "openconfig-interfaces:interfaces", "ietf-interfaces:interfaces", "openconfig-vlan:vlans", 
                    "openconfig-routing-policy:routing-policy", "openconfig-terminal-device:terminal-device"
                ]
            }
        }
    }
}
```

### Update installation parameters

To update node installation parameters it is possible to use a PUT request with updated request body that is copied
from the GET request from the previous section. It is also possible to update single parameter with direct PUT call to
specific parameter.

**CLI node**

Update multiple parameters. Specifically:
- host
- dry-run-journal-size
- journal-size

```bash
curl -X PUT \
  http://localhost:8181/rests/data/network-topology:network-topology/topology=cli/node=cliNode \
  -d 
  '{
     "node": [
        {
            "node-id": "cliNode",
            "node-extension:reconcile": false,
            "uniconfig-config:install-uniconfig-node-enabled": true,
            "cli-topology:host": "192.168.1.230",
            "cli-topology:transport-type": "ssh",
            "cli-topology:dry-run-journal-size": 170,
            "cli-topology:username": "test",
            "cli-topology:password": "test",
            "cli-topology:journal-size": 160,
            "cli-topology:port": 22,
            "cli-topology:device-version": "6.2.3",
            "cli-topology:device-type": "ios xr"
        }
     ]
  }'
```

Update single parameter:
- host

```bash
curl -X PUT \
  http://localhost:8181/rests/data/network-topology:network-topology/topology=cli/node=cliNode/cli-topology:host \
  -d 
  '{
     "cli-topology:host": "192.168.1.230"
   }'
```

**NETCONF node**

Update multiple parameters. Specifically:
- host
- keepalive-delay

```bash
curl -X PUT \
  http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=netconfNode \
  -d 
  '{
      "input": {
          "node-id": "netconfNode",
          "netconf": {
              "netconf-node-topology:host": "192.168.1.214",
              "netconf-node-topology:port": 830,
              "netconf-node-topology:keepalive-delay": 5,
              "netconf-node-topology:tcp-only": false,
              "netconf-node-topology:username": "test",
              "netconf-node-topology:password": "test",
              "uniconfig-config:uniconfig-native-enabled": true,
              "netconf-node-topology:edit-config-test-option": "test-then-set",
              "uniconfig-config:blacklist": {
                  "uniconfig-config:path": [
                      "openconfig-interfaces:interfaces", "ietf-interfaces:interfaces", "openconfig-vlan:vlans", 
                      "openconfig-routing-policy:routing-policy", "openconfig-terminal-device:terminal-device"
                  ]
              }
          }
      }
  }'
```

Update single parameter:
- host

```bash
curl -X PUT \
  http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=netconfNode/netconf-node-topology:host \
  -d 
  '{
     "netconf-node-topology:host": "192.168.1.214"
   }'
```

After these changes, when we use the GET requests from the "Show installation parameters" section, then we can see that
the parameters have actually been changed. It is also possible to use the GET request for single parameter.