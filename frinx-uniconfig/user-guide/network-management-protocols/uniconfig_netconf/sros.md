Nokia SROS devices
==================

Tested with devices SROS 13 and SROS 14.

Preliminar
----------

UniConfig Native needs the YANG models of the device to be able to
connect with it.\
Since SROS devices don't provide the possibility to automatically get
YANG models from device, it is necessary to manually copy them to the
UniConfig distribution before running.\
To do this:

> -   in UniConfig distribution folder create nested folders
>     cache/schema-sros/ (you can choose random name for nested folder,
>     it doesn't have to be 'schema-sros')
> -   copy YANG models from device into folder cache/schema-sros/

Moreover it is necessary to:

> -   copy file ignoreNodes.txt \<./ignoreNodes.txt\> into config/
>     folder of FRINX UniConfig distribution, this file contains xml
>     paths that should be ignored while removing duplicate nodes from
>     the netconf message

Optional:

> -   put file namespaceBlacklist.txt into config/ folder of FRINX
>     UniConfig distribution, this file contains xml namespaces of the
>     nodes that should be removed from the netconf message

Now UniConfig can be started.

Mount SROS device
-----------------

To mount the SROS device run:

``` {.sourceCode .bash}
curl -X PUT \
  http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=sros \
  -d '{
  "node": [
    {
      "node-id": "sros",
      "netconf-node-topology:host": "10.19.0.18",
      "netconf-node-topology:port": 2830,
      "netconf-node-topology:keepalive-delay": 10,
      "netconf-node-topology:tcp-only": false,
      "netconf-node-topology:username": "USERNAME",
      "netconf-node-topology:password": "PASSWORD",
      "uniconfig-config:uniconfig-native-enabled": true,
      "uniconfig-config:install-uniconfig-node-enabled": true,
      "uniconfig-config:blacklist": {
      "uniconfig-config:path": []
      },
      "netconf-node-topology:yang-module-capabilities": {
      "capability": ["urn:ietf:params:xml:ns:yang:ietf-inet-types?module=ietf-inet-types&amp;revision=2010-09-24",
             "urn:ietf:params:xml:ns:netconf:base:1.0?module=ietf-netconf&amp;revision=2011-06-01"]
      },
      "netconf-node-topology:customization-factory": "netconf-customization-alu"

    }
  ]
}
'
```

Where:

> -   sros: is the name of the device
> -   10.19.0.18: is the IP address of the device
> -   830: is the port number of the device
> -   USERNAME: is the username to access the device
> -   PASSWORD: is the respective password
> -   "uniconfig-config:uniconfig-native-enabled": allows to enable
>     mounting through UniConfig Native
> -   "uniconfig-config:install-uniconfig-node-enabled": allows to
>     disable mounting to uniconfig and unified layers
> -   "uniconfig-config:path": allows to specify a list of root elements
>     from models present on device to be ignored by UniConfig Native

In case of success the return code is 201.

Check if SROS device is connected
---------------------------------

To check if the device is properly connected run:

``` {.sourceCode .bash}
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=sros?conent=nonconfig
```

In case of success the return code is 200, and the response body
contains something similar to:

``` {.sourceCode .bash}
{
    "node": [
    {
        "node-id": "sros",
        "netconf-node-topology:available-capabilities": {
        "available-capability": [
            {
            "capability-origin": "device-advertised",
            "capability": "urn:ietf:params:netconf:capability:candidate:1.0"
            },
            {
            "capability-origin": "device-advertised",
            "capability": "urn:ietf:params:netconf:capability:startup:1.0"
            },
            {
            "capability-origin": "device-advertised",
            "capability": "urn:nokia.com:sros:ns:yang:sr:major-release-14"
            },

            ...
        ]
        },
        "netconf-node-topology:host": "10.19.0.18",
        "netconf-node-topology:connection-status": "connected",
        "netconf-node-topology:port": 1830
    }
    ]
}
```

Check if SROS device configuration is available in UniConfig
------------------------------------------------------------

To check if the SROS device configuration has been properly loaded in
the UniConfig config datastore, run:

``` {.sourceCode .bash}
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=sros/frinx-uniconfig-topology:configuration?content=config
```

In case of success the return code is 200 and the response body contains
something similar to:

``` {.sourceCode .bash}
{
    "frinx-uniconfig-topology:configuration": {
    "alu-conf-r13:configure": {
        "system": {
        "login-control": {
            "ssh": {
            "outbound-max-sessions": {
                "time": 10
            },
            "inbound-max-sessions": {
                "value": "50"
            }
            }
        },
        "time": {
            "sntp": {
            "shutdown": true
            },
            "zone": {
            "std-zone-name-non-std-zone-name": "UTC"
            }
        },
        "snmp": {},
        "chassis-mode": {
            "chassis-mode": "d"
        },
        "thresholds": {
            "rmon": {}
        },
        "security": {
            "per-peer-queuing": false,
            "user": [
            {
                "user-name": "admin",
                "access": {
                "netconf": true,
                "console": true

...
```
