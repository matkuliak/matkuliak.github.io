# Device Discovery

RPC device-discovery is used for verification of reachable devices in a
network. The user can check a single IP address in IPv4 format, a
network or a range of addresses. The ICMP protocol is used to check the
availability of the device. The user can also specify a specific port
(TCP or UDP) or a range of ports which are checked if they are open. The
input consists of a list of all the IP addresses that should be checked
(IPv4 or IPv6, a single IP address or a network with a prefix, or a
range of IP addresses). It also contains the desired TCP/UDP ports, that
should be checked if they are open or not on the addresses. The output
of the RPC contains the result, which shows if the IP addresses are
reachable via the ICMP protocol. Next, every IP address contains a list
of all the open TCP/UDP ports.

To test it properly you have to get your IP and add it to the
configuration JSON file. The configuration file is located under

**\~/FRINX-machine/config/uniconfig/frinx/uniconfig/config/lighty-uniconfig-config.json**

when running UniConfig stand-alone the config file is in the config
folder:

**/opt/uniconfig-frinx/config/lighty-uniconfig-config.json.**

Execute the command **ifconfig** in the terminal and look for an
interface. If you use a VPN, it's probably called **tun0**, if not, try
a different interface. From there, copy the **inet** in the interface
and paste it in the file.

```json JSON Snippet
{
"deviceDiscovery":{
        // A parameter that specifies the local address from which the scanning will be ran.
        "localAddress": "10.255.246.107",
        // A parameter that specifies the maximum pool size by the executor.
        // If left empty, the default will be CPU_COUNT * 8.
        //"maxPoolSize": 20,
        // A parameter that specifies the maximum limit of IP addresses that the service can process in one request.
        "addressCheckLimit": 254
    },
}
```

The JSON snippet contains two additional parameters.

-   The first one ("maxPoolSize") contains the value of the size of the
    executor that will be used. If the amount of addresses in the
    request is high, consider raising the value.
-   The second one ("addressCheckLimit") contains the value of how many
    addresses should be checked. If the addresses that are specified in
    the request are higher, the request will not be successful.

When you would like to discover hosts and ports in listening state in a
network, be sure not to add the network and broadcast address of that
network. For example if you want to check a network "192.168.1.0/24",
you can either use:

-   "network": "192.168.1.0/24"
-   "start-ipv4-address": "192.168.1.1", "end-ipv4-address":
    "192.168.1.254"

If you specify the range via a network statement, the network address
and broadcast address will not be included in the discovery process. If
you specify the range via the range statements, the user has to make
sure that only hosts addresses are included in the specified range.

## RPC Examples

### Successful example

RPC input contains a network with the prefix /29. The addresses in the
network and the desired ports are checked for availability.The output
contains if any addresses in the network are reachable and all the open
TCP/UDP ports.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/device-discovery:discover' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw ' {
     "input": {
         "address": [
             {
                 "network": "192.168.1.0/29"
             }
         ],
         "tcp-port": [
             {
                "start-port": 80,
                "end-port": 88
            },
            {
                "port": 443
            }
        ],
        "udp-port": [
            {
                "port": 69
            },
            {
                "start-port": 50,
                "end-port":  53
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "device": [
            {
                "host": "192.168.1.1",
                "available-udp-ports": [
                    50,
                    51,
                    52,
                    53,
                    69
                ],
                "is-host-reachable": true
            },
            {
                "host": "192.168.1.2",
                "is-host-reachable": true
            },
            {
                "host": "192.168.1.3",
                "is-host-reachable": false
            },
            {
                "host": "192.168.1.4",
                "is-host-reachable": false
            },
            {
                "host": "192.168.1.5",
                "is-host-reachable": false
            },
            {
                "host": "192.168.1.6",
                "is-host-reachable": false
            }
        ]
    }
}
```

### Successful example

RPC input contains a range of addresses. The addresses and the desired
ports are checked for availability. The output contains if any addresses
are reachable and all the open TCP/UDP ports.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/device-discovery:discover' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "address": [
            {
                "start-ipv4-address": "192.168.1.1",
                "end-ipv4-address": "192.168.1.8"
            }
        ],
        "tcp-port": [
            {
                "start-port": 80,
                "end-port": 88
            },
            {
                "port": 443
            }
        ],
        "udp-port": [
            {
                "port": 69
            },
            {
                "start-port": 50,
                "end-port":  53
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "device": [
            {
                "host": "192.168.1.1",
                "available-udp-ports": [
                    50,
                    51,
                    52,
                    53,
                    69
                ],
                "is-host-reachable": true
            },
            {
                "host": "192.168.1.2",
                "is-host-reachable": true
            },
            {
                "host": "192.168.1.3",
                "is-host-reachable": false
            },
            {
                "host": "192.168.1.4",
                "is-host-reachable": false
            },
            {
                "host": "192.168.1.5",
                "is-host-reachable": false
            },
            {
                "host": "192.168.1.6",
                "is-host-reachable": false
            },
            {
                "host": "192.168.1.7",
                "is-host-reachable": false
            },
            {
                "host": "192.168.1.8",
                "is-host-reachable": false
            }
        ]
    }
}
```

### Successful example

RPC input contains the host name and the desired ports that should be
checked for availability. The output contains if the host is reachable
and all the open TCP/UDP ports.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/device-discovery:discover' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "address": [
            {
                "hostname": "www.frinx.io"
            }
        ],
        "tcp-port": [
            {
                "start-port": 80,
                "end-port": 88
            },
            {
                "port": 443
            }
        ],
        "udp-port": [
            {
                "port": 69
            },
            {
                "start-port": 50,
                "end-port":  53
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "output": {
        "device": [
            {
                "host": "46.229.232.182",
                "available-udp-ports": [
                    50,
                    51,
                    52,
                    53,
                    69
                ],
                "available-tcp-ports": [
                    80,
                    443
                ],
                "is-host-reachable": true
            }
        ]
    }
}
```

### Failed Example

RPC input contains two addresses that are incorrectly wrapped.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/device-discovery:discover' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "address": [
            {
                "hostname": "www.frinx.io",
                "ip-address": "46.229.232.182"
            }
        ],
        "tcp-port": [
            {
                "start-port": 80,
                "end-port": 88
            },
            {
                "port": 443
            }
        ],
        "udp-port": [
            {
                "port": 69
            },
            {
                "start-port": 50,
                "end-port":  53
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "errors": {
        "error": [
            {
                "error-type": "protocol",
                "error-tag": "malformed-message",
                "error-message": "Error parsing input: Data from case (urn:ietf:params:xml:ns:yang:rpc?revision=2021-07-08)ip-address-case are specified but other data from case (urn:ietf:params:xml:ns:yang:rpc?revision=2021-07-08)hostname-case were specified earlier. Data aren't from the same case.",
                "error-info": "Data from case (urn:ietf:params:xml:ns:yang:rpc?revision=2021-07-08)ip-address-case are specified but other data from case (urn:ietf:params:xml:ns:yang:rpc?revision=2021-07-08)hostname-case were specified earlier. Data aren't from the same case."
            }
        ]
    }
}
```

### Failed Example

RPC input contains an IP range where the start point is greater than end
point.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/device-discovery:discover' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "address": [
            {
                "start-ipv4-address": "127.0.0.63",
                "end-ipv4-address": "127.0.0.60"
            }
        ],
        "tcp-port": [
            {
                "start-port": 80,
                "end-port": 88
            },
            {
                "port": 443
            }
        ],
        "udp-port": [
            {
                "port": 69
            },
            {
                "start-port": 50,
                "end-port":  53
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "errors": {
        "error": [
            {
                "error-type": "protocol",
                "error-tag": "bad-element",
                "error-message": "Invalid IP address range! End address should be bigger than start address! The range is from 127.0.0.63 to 127.0.0.60"
            }
        ]
    }
}
```

### Not supported operation Example

RPC input contains a network in IPv6 format that is currently not
supported.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/device-discovery:discover' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "address": [
            {
                "network": "2001:db8:a::123/64"
            }
        ],
        "tcp-port": [
            {
                "start-port": 80,
                "end-port": 88
            },
            {
                "port": 443
            }
        ],
        "udp-port": [
            {
                "port": 69
            },
            {
                "start-port": 50,
                "end-port":  53
            }
        ]
    }
}'
```

```json RPC Response, Status: 200
{
    "errors": {
        "error": [
            {
                "error-type": "application",
                "error-tag": "operation-not-supported",
                "error-message": "IPv6 is not supported yet!"
            }
        ]
    }
}
```
