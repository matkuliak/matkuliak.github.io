IP Infusion OcNOS Devices
=========================

Mounting a OcNos device with UniConfig
--------------------------------------

This is the request to mount a OcNos device:

``` {.sourceCode .bash}
curl PUT \
  'https://192.168.60.53:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=ocnos' \
  'Authorization: Basic YWRtaW46YWRtaW4=' \
  'Content-Type: application/json' \
  '{
    "node": [
      {
        "node-id": "ocnos",
        "netconf-node-topology:host": "192.168.1.248",
        "node-extension:reconcile": false,
        "netconf-node-topology:sleep-factor": 1,
        "netconf-node-topology:between-attempts-timeout-millis": 10000,
        "netconf-node-topology:connection-timeout-millis": 30000,
        "netconf-node-topology:keepalive-delay": 10,
        "netconf-node-topology:port": 830,
        "netconf-node-topology:tcp-only": false,
        "netconf-node-topology:username": "ocnos",
        "netconf-node-topology:password": "ocnos",
        "uniconfig-config:uniconfig-native-enabled": true
      }
    ]
  }'
```

Where:

> -   "node-id": is the name of the device
> -   "netconf-node-topology:host" (192.168.1.248): is the ip address of
>     the device
> -   "netconf-node-topology:port" (830): is the port number of the
>     device
> -   "netconf-node-topology:username" (ocnos): is the username to
>     access the device
> -   "netconf-node-topology:password" (ocnos): is the respective
>     password
> -   "uniconfig-config:uniconfig-native-enabled": allows to enable
>     mounting through UniConfig Native

### Show configuration

To show all the configurations loaded in config datastore, run:

``` {.sourceCode .bash}
curl -X GET \
  https://{{odl_ip}}:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=ocnos

   {
       "node": [
           {
               "node-id": "ocnos",
               "frinx-uniconfig-topology:connection-status": "installed",
               "frinx-uniconfig-topology:status-message": "installed uniconfig node",
               "frinx-uniconfig-topology:configuration": {
                   "ipi-interface:interfaces": {
                       "interface": [
                           {
                               "name": "lo",
                               "config": {
                                   "name": "lo"
                               },
                               "ipi-if-ip:ipv6": {
                                   "addresses": [
                                       {
                                           "ipv6-address": "::1/128",
                                           "config": {
                                               "ipv6-address": "::1/128"
                                           }
                                       }
                                   ]
                               },
                               "ipi-if-ip:ipv4": {
                                   "config": {
                                       "primary-ip-addr": "127.0.0.1/8"
                                   }
                               }
                           },
                           {
                               "name": "eth4",
                               "config": {
                                   "name": "eth4"
                               }
                           },
                           {
                               "name": "eth2",
                               "config": {
                                   "name": "eth2"
                               }
                           },
                           {
                               "name": "eth3",
                               "config": {
                                   "name": "eth3"
                               }
                           },
                           {
                               "name": "eth0",
                               "config": {
                                   "name": "eth0",
                                   "vrf-name": "default"
                               },
                               "ipi-if-ip:ipv4": {
                                   "config": {
                                       "primary-ip-addr": "192.168.1.248/24"
                                   }
                               }
                           },
                           {
                               "name": "eth1",
                               "config": {
                                   "name": "eth1"
                               }
                           }
                       ],
                       "ipi-if-extended:global": {
                           "error-disable": {
                               "config": {
                                   "error-disable-stp-bpdu-guard": true
                               }
                           }
                       }
                   },
                   "ipi-dhcp:dhcp": {
                       "relay": {
                           "global": {
                               "config": {
                                   "enable-dhcpv4-relay": [
                                       null
                                   ],
                                   "enable-dhcpv6-relay": [
                                       null
                                   ]
                               }
                           }
                       }
                   },
                   "ipi-logging:logging": {
                       "rsyslog": [
                           {
                               "vrf": "default",
                               "config": {
                                   "enable-rsyslog": "rsyslog",
                                   "vrf": "default"
                               }
                           }
                       ]
                   },
                   "ietf-netconf-acm:nacm": {},
                   "ipi-network-instance:network-instances": {
                       "network-instance": [
                           {
                               "instance-name": "management",
                               "instance-type": "vrf",
                               "ipi-vrf:vrf": {
                                   "config": {
                                       "vrf-name": "management"
                                   }
                               },
                               "config": {
                                   "instance-name": "management",
                                   "instance-type": "vrf"
                               }
                           },
                           {
                               "instance-name": "default",
                               "instance-type": "vrf",
                               "ipi-vrf:vrf": {
                                   "config": {
                                       "vrf-name": "default"
                                   }
                               },
                               "config": {
                                   "instance-name": "default",
                                   "instance-type": "vrf"
                               }
                           }
                       ]
                   }
               }
           }
       ]
   }
```

Troubleshooting OcNos mounting with UniConfig
---------------------------------------------

If you have trouble mounting an OcNOS device into UniConfig, please
consult ocnos-tshoot
