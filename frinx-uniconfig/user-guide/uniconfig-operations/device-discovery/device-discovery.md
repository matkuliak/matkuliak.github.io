Device Discovery
================

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

\~/FRINX-machine/config/uniconfig/frinx/uniconfig/config/lighty-uniconfig-config.json

when running UniConfig stand-alone the config file is in the config
folder:

/opt/uniconfig-frinx/config/lighty-uniconfig-config.json.

Execute the command **ifconfig** in the terminal and look for an
interface. If you use a VPN, it's probably called **tun0**, if not, try
a different interface. From there, copy the **inet** in the interface
and paste it in the file.

> **JSON snippet**
>
> **JSON:**

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

RPC Examples
------------

### Successful example

RPC input contains a network with the prefix /29. The addresses in the
network and the desired ports are checked for availability.The output
contains if any addresses in the network are reachable and all the open
TCP/UDP ports.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Successful example

RPC input contains a range of addresses. The addresses and the desired
ports are checked for availability. The output contains if any addresses
are reachable and all the open TCP/UDP ports.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Successful example

RPC input contains the host name and the desired ports that should be
checked for availability. The output contains if the host is reachable
and all the open TCP/UDP ports.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input contains two addresses that are incorrectly wrapped.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

### Failed Example

RPC input contains an IP range where the start point is greater than end
point.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *

Not supported operation Example ++++++++++++++

RPC input contains a network in IPv6 format that is currently not
supported.

> **RPC Request**
>
> **RPC request:**

> **RPC Response**
>
> **RPC response:**

* * * * *
