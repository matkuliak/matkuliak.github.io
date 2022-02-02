---
order: 6
---

# Translation Units Documentation for FRINX Uniconfig

## Auto-generated documentation

A documentation to translation-units that is generated automatically
from the source code and javadocs can be found [here](https://frinxio.github.io/translation-units-docs/). 
This documentation is useful to check actual implementations, whether a functionality is
implemented for a particular device and by which protocol (netconf or
cli).

## Manual documentation

[This repository](https://github.com/FRINXio/translation-units-docs)
contains documentation for all available translation units. A
translation unit is a piece of code that includes handlers to read from
or write to a specific device (e.g. Cisco IOS classic router) and
facilitates the translation in OpenConfig models. The purpose of this
documentation is to see which commands can be read and set and how they
map to the respective YANG models. Every section has a README file that
provides an overview of all show and configuration commands that are
supported.

### OPERATIONAL datasets

[Go to operational
datasets](https://github.com/FRINXio/translation-units-docs/blob/master/Operational%20datasets/README.md)

Show commands are commands that usually on Cisco device start with
'show'. The aim is to obtain data from the router.

#### URL

GET operation issued on operational datastore

#### OPENCONFIG YANG

In case of show commands this section is a sample output of a particular
show command.

#### OS COMMANDS

In this section we list the actual router commands with sample outputs,
where the data obtained and transformed into OpenConfig YANG is marked
as bold. We list show commands and outputs for each supported device OS.

IOS XR | IOS Classic/XE | Junos

#### DEVICE YANG

In case of CLI units, the unit parses the output of the CLI command
directly into OC YANG. In case of Netconf units, the output is mapped to
OC YANG through Device YANG (YANG model supported by the device). In
case of Netconf units, the YANG is also written in documentation. This
section is a link to XML unit test input testing this operation.

#### UNIT

Link to github code where this show command is implemented along with
unit version range.

### CONFIGURATION datasets

[Go to config
datasets](https://github.com/FRINXio/translation-units-docs/blob/master/Configuration%20datasets/README.md)

#### URL

PUT operation with given URL will result in creating of data in config
datastore DELETE operation with given URL will result in removing data
in config datastore

#### OPENCONFIG YANG

In case of configuration commands, this section represents the HTTP body
in PUT operation

#### OS COMMANDS

In this section we list the actual router commands that are mapped to
the OpenConfig YANG model. Data transformed into OpenConfig YANG is
marked as bold. We list commands for each supported device OS.

IOS XR | IOS Classic/XE | Junos

#### DEVICE YANG

In case of Netconf units, the device yang represents command sent to the
device in device YANG model. This section is a link to XML unit test
input testing this configuration.

#### UNIT

Link to github code where this config command is implemented along with
unit version range.
