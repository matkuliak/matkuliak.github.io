---
label: Getting started
icon: rocket
order: 10000
---

# FRINX UniConfig introduction

The purpose of UniConfig is to manage configuration state and to
retrieve operational state of physical and virtual networking devices.
UniConfig provides a single API for many different devices in the
network. UniConfig can be run as an application on bare metal in a VM or
in a container, standalone or as part of our automation solution FRINX
Machine. UniConfig has a built-in data store that can be run in memory
or with an external database.

## UniConfig key feature overview

-   Retrieves and stores current startup and running configuration from
    mounted network devices
-   Pushes configuration data to devices via NETCONF or CLI
-   Allows for diffs to be built between actual and intended execution
    of atomic configuration changes
-   Retrieves operational data from devices via NETCONF or CLI
-   Provides subtree filtering capabilities in NETCONF
-   Transactions can be managed on one or multiple devices
-   Offers the ability to do a dry-commit to evaluate the functionality
    of a configuration before it is executed on devices
-   Provides snapshots of previous configurations if you need to
    rollback
-   Translates between CLI, native model and standard data models (i.e.
    OpenConfig) via our open-source device library
    (<https://github.com/FRINXio/cli-units>)
-   A 'Lazy CLI' feature to suspend and resume connections without
    having to maintain keepalives
-   Can read and store proprietary data models from network devices that
    follow the YANG data model
-   Data export and import via blacklist and whitelist functions
-   Provides templates for device configuration
-   Supports PostgreSQL as an external database
-   Support for YANG 1.1 and Tail-f actions
-   Subscription to NETCONF notifications via web sockets
-   Support for 3-phase commit by using NETCONF confirmed-commit
-   High availability
-   The ability to log specific devices as needed
-   Can execute commands in parallel on multiple devices
-   Choose between NETCONF or RESTCONF to connect to devices
-   The UniConfig client allows for simple, full-service access to the
    UniConfig features
-   Python microservices are used to integrate with the FRINX machine
-   The UniConfig UI allows users to interact with the network
    controller through a web-based user interface

### UniConfig enables users to communicate with their network infrastructure via four options:

1)  **Execute & Read API** - Unstructured data via SSH and Telnet
2)  **OpenConfig API** – Translation provided by our open source device
    library
3)  **UniConfig Native API** – Direct access to vendor specific YANG
    data models that are native to the connected devices as well as
    UniConfig functions (i.e. diff, commit, snapshots, etc.)
4)  **UniConfig Native CLI API** – Programmatic access to the CLI
    without the need for translation units (experimental)

**Execute & Read capable API:** Like Ansible, TCL Scripting or similar
products strings can be passed and received through SSH or Telnet via
REST API. UniConfig provides the authentication and transportation of
data without interpreting it.

**OpenConfig API:** An API that is translated into device specific CLI
or YANG data models. The installation of "translation units" on devices
is required. FRINX provides an open source library of devices from a
variety of network vendors. The open source framework allows anyone to
contribute or consume the contents of the expanding list of supported
network devices.

**UniConfig Native API:** A vendor specific YANG data models are
absorbed by UniConfig to allow configuration of mounted devices.
UniConfig maps vendor specific "native" models into it's data store to
provide stateful configuration capabilities to applications and users.

**UniConfig Native CLI API:** Allows for interaction with a devices CLI
is programmatic through the API without the use of 'translation units',
only a schema file is needed. (This option is currently experimental,
contact FRINX for more information.)

![UniConfig solution](FRINX_Uniconfig_solution.jpg)

## UniConfig in a Docker container

### Download and activate FRINX UniConfig

Enter the following commands to download, activate and start UniConfig
in a Docker container:

```
docker pull frinx/uniconfig:4.2.9
TOKEN=[frinx-license_secret-token]
docker run -it --hostname uniconfig --name uniconfig -p 8181:8181 frinx/uniconfig:4.2.9 -l $TOKEN
```

!!!
Replace [frinx-licence-secret-token] with your unique token. The token is unique to your user account on frinx.io and cannot be shared with other users. It can be found [here](https://frinx.io/profile). (you need to be logged in frinx.io to view your token).
!!!

### Stop the container


To stop the container type:

```
docker stop uniconfig
```

## UniConfig as a Java process in a VM or on a host

### Download FRINX UniConfig

Click on the link to download a zip archive of the latest FRINX
UniConfig:\
[uniconfig-4.2.9.frinx.zip](https://license.frinx.io/download/uniconfig-4.2.9.frinx.zip)\
By downloading the file you accept the FRINX software agreement:
[EULA](https://frinx.io/eula)

### Activate FRINX UniConfig

To activate UniConfig, unzip the file, open the directory and run the
following command:

```
./run_uniconfig.sh -l [frinx-license-secret-token]
```

!!!
Replace [frinx-licence-secret-token] with your unique token. The token is unique to your user account on frinx.io and cannot be shared with other users. It can be found [here](https://frinx.io/profile). (you need to be logged in frinx.io to view your token).
!!!

!!!
For more information on the different arguments run the startup script with the **-h** flag
!!!

## Offline Activation

Please contact **support@frinx.io** for offline activation of
UniConfig.
