# Juniper Junos devices

!!!
If you have trouble with installing Junos device into UniConfig, please
consult [Why I can not install Junos device on UniConfig
?](https://docs.frinx.io/frinx-uniconfig/q_a/#why-i-can-not-install-junos-device-on-uniconfig-)
!!!

## Install Junos device

This is the request to install Junos device:

```bash
curl -X PUT \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=junos \
 -d '{
 "node": [
   {
     "node-id": "junos",
     "netconf-node-topology:host": "10.10.199.47",
     "netconf-node-topology:port": 830,
     "netconf-node-topology:keepalive-delay": 50000,
     "netconf-node-topology:tcp-only": false,
     "netconf-node-topology:username": "USERNAME",
     "netconf-node-topology:password": "PASSWORD",
     "uniconfig-config:uniconfig-native-enabled": true,
     "uniconfig-config:install-uniconfig-node-enabled": true,
     "uniconfig-config:blacklist": {
         "uniconfig-config:path": []
     }
   }
  ]
}'
```

**Where:**

-   junos: is the name of the device
-   10.10.199.47: is the ip address of the device
-   830: is the port number of the device
-   USERNAME: is the username to access the device
-   PASSWORD: is the respective password
-   "uniconfig-config:uniconfig-native-enabled": allows to enable
     installing through UniConfig Native
-   "uniconfig-config:install-uniconfig-node-enabled": allows to
     disable installing to uniconfig and unified layers
-   "uniconfig-config:path": allows to specify a list of root elements
     from models present on device to be ignored by UniConfig Native

## Show configuration

To show all the configurations loaded in config datastore, run:

```bash
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=junos?content=config
```

In case of success it will respond something similar to:

```bash
{
 "node": [
     {
         "node-id": "junos",
         "frinx-uniconfig-topology:configuration": {
             "configuration:configuration": {
                 "interfaces": {
                     "interface": [
                         {
                             "name": "fxp0",
                             "unit": [
                                 {
                                     "name": "0",
                                     "family": {
                                         "inet": {
                                             "dhcp": {
                                                 "vendor-id": "Juniper-vmx"
                                             }
                                         }
                                     }
                                 }
                             ]
                         },
                         {
                             "name": "ge-0/0/2",
                             "disable": [
                                 null
                             ]
                         },
                         {
                             "name": "ge-0/0/3",
                             "disable": [
                                 null
                             ]
                         },
                         {
                             "name": "ge-0/0/0",
                             "disable": [
                                 null
                             ]
                         },
                         {
                             "name": "ge-0/0/1",
                             "disable": [
                                 null
                             ]
                         }
                     ]
                 },

         ...
```

## Show interface configuration

To show the configuration related to a specific interface, in this case
“ge-0/0/2”, run:

```bash
curl -X GET \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=junos/frinx-uniconfig-topology:configuration/configuration:configuration/interfaces/interface=ge-0%2F0%2F2?content=config
```

The response will show the status of the interface:

```bash
{
 "interface": [
     {
         "name": "ge-0/0/2",
         "disable": [
             null
         ]
     }
 ]
```

## Enable interface in configuration

To enable the interface “ge-0/0/2” in config datastore, run:

```bash
curl -X PUT \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=junos/frinx-uniconfig-topology:configuration/configuration:configuration/interfaces/interface=ge-0%2F0%2F2 \
 -d '{
   "interface": [
       {
           "name": "ge-0/0/2"
       }
   ]
}'
```

## Disable interface in configuration

To disable the interface “ge-0/0/2” in config datastore, run:

```bash
curl -X PUT \
 http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=junos/frinx-uniconfig-topology:configuration/configuration:configuration/interfaces/interface=ge-0%2F0%2F2 \
 -d '{
   "interface": [
       {
           "name": "ge-0/0/2",
           "disable": [
               null
           ]
       }
   ]
}'
```

After the configuration changes have been done on the config datastore,
it is possible to send to the Junos device with the commit request.
