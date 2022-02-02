# RPC install-multiple-nodes

This RPC installs multiple devices at once. It uses the default
install-node RPC. Devices are installed in parallel.

## RPC Examples

### Successful example

RPC input contains two devices (R1 and R2).

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:install-multiple-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "nodes": [
            {
                "node-id": "R1",
                "cli": {
                    "cli-topology:host": "192.168.1.214",
                    "cli-topology:port": "22",
                    "cli-topology:transport-type": "ssh",
                    "cli-topology:device-type": "ios xr",
                    "cli-topology:device-version": "5.3.4",
                    "cli-topology:username": "cisco",
                    "cli-topology:password": "cisco",
                    "cli-topology:journal-size": 150,
                    "cli-topology:dry-run-journal-size": 150,
                    "node-extension:reconcile": false,
                    "uniconfig-config:install-uniconfig-node-enabled": true
                }
            },
            {
                "node-id": "R2",
                "cli": {
                    "cli-topology:host": "192.168.1.211",
                    "cli-topology:port": "22",
                    "cli-topology:transport-type": "ssh",
                    "cli-topology:device-type": "ios xr",
                    "cli-topology:device-version": "6.1.2",
                    "cli-topology:username": "cisco",
                    "cli-topology:password": "cisco",
                    "cli-topology:dry-run-journal-size": 180,
                    "cli-topology:journal-size": 150,
                    "uniconfig-config:install-uniconfig-node-enabled": true,
                    "node-extension:reconcile": false
                }
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

RPC input contains devices (R1 and R2) and R2 uses two different
protocols.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:install-multiple-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "nodes": [
            {
                "node-id": "R2",
                "cli": {
                    "cli-topology:host": "192.168.1.211",
                    "cli-topology:port": "22",
                    "cli-topology:transport-type": "ssh",
                    "cli-topology:device-type": "ios xr",
                    "cli-topology:device-version": "6.1.2",
                    "cli-topology:username": "cisco",
                    "cli-topology:password": "cisco",
                    "cli-topology:dry-run-journal-size": 180,
                    "cli-topology:journal-size": 150,
                    "uniconfig-config:install-uniconfig-node-enabled": false,
                    "node-extension:reconcile": false
                },
                "netconf": {
                    "netconf-node-topology:host": "192.168.1.211",
                    "netconf-node-topology:port": 830,
                    "netconf-node-topology:keepalive-delay": 0,
                    "netconf-node-topology:tcp-only": false,
                    "netconf-node-topology:username": "cisco",
                    "netconf-node-topology:password": "cisco",
                    "netconf-node-topology:dry-run-journal-size": 180
                }
            },
            {
                "node-id": "R1",
                "cli": {
                    "cli-topology:host": "192.168.1.214",
                    "cli-topology:port": "22",
                    "cli-topology:transport-type": "ssh",
                    "cli-topology:device-type": "ios xr",
                    "cli-topology:device-version": "5.3.4",
                    "cli-topology:username": "cisco",
                    "cli-topology:password": "cisco",
                    "cli-topology:journal-size": 150,
                    "cli-topology:dry-run-journal-size": 150,
                    "node-extension:reconcile": false

                }
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

RPC input contains two devices (R1 and R2) and R2 is already installed
using CLI protocol.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:install-multiple-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "nodes": [
            {
                "node-id": "R1",
                "cli": {
                    "cli-topology:host": "192.168.1.214",
                    "cli-topology:port": "22",
                    "cli-topology:transport-type": "ssh",
                    "cli-topology:device-type": "ios xr",
                    "cli-topology:device-version": "5.3.4",
                    "cli-topology:username": "cisco",
                    "cli-topology:password": "cisco",
                    "cli-topology:journal-size": 150,
                    "cli-topology:dry-run-journal-size": 150,
                    "node-extension:reconcile": false,
                    "uniconfig-config:install-uniconfig-node-enabled": true
                }
            },
            {
                "node-id": "R2",
                "cli": {
                    "cli-topology:host": "192.168.1.211",
                    "cli-topology:port": "22",
                    "cli-topology:transport-type": "ssh",
                    "cli-topology:device-type": "ios xr",
                    "cli-topology:device-version": "6.1.2",
                    "cli-topology:username": "cisco",
                    "cli-topology:password": "cisco",
                    "cli-topology:dry-run-journal-size": 180,
                    "cli-topology:journal-size": 150,
                    "uniconfig-config:install-uniconfig-node-enabled": true,
                    "node-extension:reconcile": false
                }
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
                "error-message": "Node has already been installed using CLI protocol",
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
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:install-multiple-nodes' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "nodes": [
            {
                "cli": {
                    "cli-topology:host": "192.168.1.214",
                    "cli-topology:port": "22",
                    "cli-topology:transport-type": "ssh",
                    "cli-topology:device-type": "ios xr",
                    "cli-topology:device-version": "5.3.4",
                    "cli-topology:username": "cisco",
                    "cli-topology:password": "cisco",
                    "cli-topology:journal-size": 150,
                    "cli-topology:dry-run-journal-size": 150,
                    "node-extension:reconcile": false,
                    "uniconfig-config:install-uniconfig-node-enabled": true
                }
            },
            {
                "node-id": "R2",
                "cli": {
                    "cli-topology:host": "192.168.1.211",
                    "cli-topology:port": "22",
                    "cli-topology:transport-type": "ssh",
                    "cli-topology:device-type": "ios xr",
                    "cli-topology:device-version": "6.1.2",
                    "cli-topology:username": "cisco",
                    "cli-topology:password": "cisco",
                    "cli-topology:dry-run-journal-size": 180,
                    "cli-topology:journal-size": 150,
                    "uniconfig-config:install-uniconfig-node-enabled": true,
                    "node-extension:reconcile": false
                }
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
