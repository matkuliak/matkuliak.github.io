Cisco IOS XR devices
====================

Mount Cisco XR device
---------------------

Cisco XR device can be mounted through UniConfig Native with the
following request:

``` {.sourceCode .bash}
curl -X PUT \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=R1 \
 -d '{
 "node": [
   {
     "node-id": "R1",
     "netconf-node-topology:host": "192.168.1.214",
     "netconf-node-topology:port": 830,
     "netconf-node-topology:keepalive-delay": 0,
     "netconf-node-topology:tcp-only": false,
     "netconf-node-topology:username": "USERNAME",
     "netconf-node-topology:password": "PASSWORD",
     "uniconfig-config:uniconfig-native-enabled": true,
     "uniconfig-config:install-uniconfig-node-enabled": true,
     "uniconfig-config:blacklist": {
         "uniconfig-config:path": ["openconfig-interfaces:interfaces", "ietf-interfaces:interfaces", "openconfig-vlan:vlans", "openconfig-routing-policy:routing-policy"]
     }
   }
  ]
}'
```

Where:

> -   R1: is the name of the device
> -   192.168.1.214: is the IP address of the device
> -   830: is the port number of the device
> -   USERNAME: is the username to access the device
> -   PASSWORD: is the respective password
> -   "uniconfig-config:uniconfig-native-enabled": allows to enable
>     mounting through UniConfig Native
> -   "uniconfig-config:install-uniconfig-node-enabled": allows to
>     disable mounting to uniconfig and unified layers
> -   "uniconfig-config:path": allows to specify a list of root elements
>     from models present on device to be ignored by UniConfig Native

Check if Cisco XR device is mounted succesfully
-----------------------------------------------

After the device has been mounted, the connection can be checked with
the following command:

``` {.sourceCode .bash}
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=R1?content=nonconfig
```

In case the device is still connecting console will return:

``` {.sourceCode .bash}
{
   "node": [
       {
           "node-id": "R1",
           "netconf-node-topology:host": "192.168.1.214",
           "netconf-node-topology:connection-status": "connecting",
           "netconf-node-topology:port": 830
       }
   ]
}
```

Send again the same GET request until the device will be connected.\
When the device is connected, the response is similar to:

``` {.sourceCode .bash}
{
 "node": [
     {
         "node-id": "R1",
         "netconf-node-topology:unavailable-capabilities": {
             "unavailable-capability": [
                 ...
                 {
                     "capability": "(http://openconfig.net/yang/bgp?revision=2015-05-15)bgp",
                     "failure-reason": "unable-to-resolve"
                 },
                 {
                     "capability": "(http://cisco.com/ns/yang/Cisco-IOS-XR-shellutil-filesystem-oper?revision=2015-11-09)Cisco-IOS-XR-shellutil-filesystem-oper-sub1",
                     "failure-reason": "missing-source"
                 },
                 ...
              ]
         },
         "netconf-node-topology:available-capabilities": {
             "available-capability": [
                 ...
                 {
                     "capability-origin": "device-advertised",
                     "capability": "urn:ietf:params:netconf:capability:confirmed-commit:1.1"
                 },
                                     {
                     "capability-origin": "device-advertised",
                     "capability": "(http://cisco.com/ns/yang/Cisco-IOS-XR-tty-server- oper?revision=2015-01-07)Cisco-IOS-XR-tty-server-oper"
                 },


             ...
             ]
         },
         "netconf-node-topology:host": "192.168.1.214",
         "netconf-node-topology:connection-status": "connected",
         "netconf-node-topology:port": 830
     }
 ]
```

> }

This response body shows which are the available capabilities that have
been properly loaded and which are instead the unavailable capabilities
that have not been loaded with the related failing reason.

Check if Cisco XR device configuration is available in UniConfig
----------------------------------------------------------------

The following command checks that the configuration of the device is
available in UniConfig:

``` {.sourceCode .bash}
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=R1/frinx-uniconfig-topology:configuration?content=config
```

The example of output:

``` {.sourceCode .bash}
{
 "frinx-uniconfig-topology:configuration": {
     "Cisco-IOS-XR-crypto-sam-cfg:crypto": {
         "Cisco-IOS-XR-crypto-ssh-cfg:ssh": {
             "server": {
                 "v2": [
                     null
                 ],
                 "netconf": 830
             }
         }
     },
     "Cisco-IOS-XR-ifmgr-cfg:interface-configurations": {
         "interface-configuration": [
             {
                 "active": "act",
                 "interface-name": "GigabitEthernet0/0/0/5",
                 "shutdown": [
                     null
                 ]
             },
             {
                 "active": "act",
                 "interface-name": "GigabitEthernet0/0/0/4",
                 "shutdown": [
                     null
                 ]
             },
             {
                 "active": "act",
                 "interface-name": "GigabitEthernet0/0/0/3",
                 "shutdown": [
                     null
                 ]
             },
             {
                 "active": "act",
                 "interface-name": "GigabitEthernet0/0/0/2",
                 "shutdown": [
                     null
                 ]
             },
             {
                 "active": "act",
                 "interface-name": "GigabitEthernet0/0/0/1",
                 "shutdown": [
                     null
                 ]
             },
             {
                 "active": "act",
                 "interface-name": "GigabitEthernet0/0/0/0",
                 "description": "testing interface"
             },
             {
                 "active": "act",
                 "interface-name": "MgmtEth0/0/CPU0/0",
                 "Cisco-IOS-XR-ipv4-io-cfg:ipv4-network": {
                     "addresses": {
                         "primary": {
                             "address": "192.168.1.214",
                             "netmask": "255.255.255.0"
                         }
                     }
                 }
             }
         ]
     },
     "Cisco-IOS-XR-man-netconf-cfg:netconf-yang": {
         "agent": {
             "ssh": {
                 "enable": [
                     null
                 ]
             }
         }
     }
 }
```

> }

Check if Cisco XR device has an existing interface
--------------------------------------------------

It is possible to check if an interface is available on a device by
checking if it is available on the operational datastore. To check if
the interface Loopback123 is available on device R1 run:

``` {.sourceCode .bash}
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=R1/frinx-uniconfig-topology:configuration/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=act,Loopback123?content=nonconfig
```

If the interface exists the response is:

``` {.sourceCode .bash}
{
   "interface-configuration": [
       {
           "active": "act",
           "interface-name": "Loopback123",
           "description": "description from UniConfig Native",
           "interface-virtual": [
               null
           ]
       }
   ]
}
```

If the interface doesn't exist the return code is 404.

Unmount device
--------------

To unmount device R1 run:

``` {.sourceCode .bash}
curl -X DELETE \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=R1
```

In case of success the return code is 204, otherwise is 404.

Can be used for instance this
[request](#check-if-cisco-xr-device-is-mounted-succesfully) to check if
the device has been properly unmounted.\
In this case the return code must be 404 since the device does not exist
in UniConfig anymore.
