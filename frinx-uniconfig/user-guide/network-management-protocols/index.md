---
label: Device Installation
order: 9000
---


# Device installation

**Device installation**

Guide explaining installation mechanisms along with both CLI and NETCONF
examples.

**UniConfig CLI**

The CLI southbound plugin enables the Frinx UniConfig to communicate
with CLI devices that do not speak NETCONF or any other programmatic
API. The CLI service module uses YANG models and implements a
translation logic to send and receive structured data to and from CLI
devices.

**UniConfig Netconf**

NETCONF is an Internet Engineering Task Force (IETF) protocol used for
configuration and monitoring devices in the network. It can be used to
“create, recover, update, and delete configurations of network
devices”.NETCONF operations are overlaid on the Remote Procedure Call
(RPC) layer and may be described in either XML or JSON.

**UniConfig-native CLI**

UniConfig-native CLI allows user configuration of CLI-enabled devices
using YANG models that describe configuration commands. In
UniConfig-native CLI deployment translation units are defined only by
YANG models and device-specific characteristics that are used for
parsing and serialization of commands. Afterwards, readers and writers
are automatically created and provided to translation registry - user
doesn’t write them individually. YANG models can be constructed by
following of well-defined rules that are explained in Developer Guide.

Network management protocols are used in southbound API of UniConfig
Lighty distribution for device installation and communication.
Currently, following protocols are supported:

1.  NETCONF (Network Configuration Protocol)
2.  SSH / TELNET

