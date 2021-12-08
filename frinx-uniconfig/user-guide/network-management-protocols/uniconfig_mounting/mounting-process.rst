Mounting of devices
===================

Mounting processes
------------------

Creation of the mount-point
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following sequence diagram depicts processes that are executed after creation of CLI or NETCONF mount-point.

.. image:: CLI_NETCONF_mount.png
   :target: /_images/CLI_NETCONF_mount.png
   :alt: Mount CLI or NETCONF device

Config initialization
~~~~~~~~~~~~~~~~~~~~~

Next sequence diagram describes reading of data from device using Unified mount-point and pushing of device configuration into datastores under UniConfig node.

.. image:: initial_read.png
   :target: /_images/initial_read.png
   :alt: Initial reading

Initial reading of network element configuration is triggered after unified node is created in operational datastore. Then, reading of data from network element is delegated to unified mountpoint. If UniConfig node already exists in operational datastore, data is stored only to operational datastore (e.g. in case of reconnect). In other case, data is stored as UniConfig node to both config and operational datastore.

Mounting of CLI device
----------------------

Description of mount-request parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Identification of remote device
###############################

List of basic connection parameters that are used for identification of remote device. All of these mount-point parameters are mandatory.

* **network-topology:node-id** - Name of node that represents device / mount-point in the topology.
* **cli-topology:host** - IP address or domain-name of target device that runs SSH or Telnet server.
* **cli-topology:transport-type** - Application protocol used for communication with device - supported options are 'ssh' and 'telnet'.
* **cli-topology:port** - TCP port on which the SSH or Telnet server on remote device is listening to incoming connections. Standard SSH port is '22', standard Telnet port is '23'.
* **cli-topology:device-type** - Device type that is used for selection of translation units that maps device configuration to OpenConfig models. Currently, 'junos', 'ios xr', 'ios', 'nexus', 'ironware' (Brocade) and 'vrp' (Huawei) options are supported.
* **cli-topology:device-version** - Version of device. Use a specific version or * for a generic one. * enables only basic read and write management without the support of OpenConfig models.

.. note::

    The complete list of supported 'device-type' and 'device-version' parameters can be obtained using following HTTP request:

    .. code-block:: guess

        GET /rests/data/cli-translate-registry:available-cli-device-translations?content=nonconfig

Authentication parameters
#########################

List of authentication parameters used for identification of remote user utilized for configuration of the device. Username and password parameters are mandatory.

* **cli-topology:username** - Username for accessing of CLI management line.
* **cli-topology:password** - Password assigned to username.
* **ios-enable-password** - When you mount a device, you can also specify its password/secret which is used (mostly on Cisco devices) to access privileged mode. By default, if a Cisco device is not in privileged mode when connected to, the secret is used to enter privileged mode. If there is no secret set, the cli-topology:password will be used.

List of parameters that can be used for adjusting of reconnection strategy. None of these parameters is mandatory - if they are not set, default values are set. There are two exclusive groups of parameters based on selected reconnection strategy - you can define only parameters from single group. By default, keepalive strategy is used.

Keepalive strategies
####################

**1. Keepalive reconnection strategy**

    * **cli-topology:keepalive-delay** - Delay between sending of keepalive messages over CLI session. Default value: 60 seconds.
    * **cli-topology:keepalive-timeout** - This parameter defines how much time CLI layer should wait for response to keepalive message before the session is closed. Default value: 60 seconds.
    * **cli-topology:keepalive-initial-delay** - This parameter defines how much time CLI layer waits for establishment of new CLI session before the first reconnection attempt is launched. Default value: 120 seconds.

**2. Lazy reconnection strategy**

    * **cli-topology:command-timeout** - Maximal time (in seconds) for command execution. If a command cannot be executed on a device in this time, the execution is considered a failure. Default value: 60 seconds.
    * **cli-topology:connection-establish-timeout** - Maximal time (in seconds) for connection establishment. If a connection attempt fails in this time, the attempt is considered a failure. Default value: 60 seconds.
    * **cli-topology:connection-lazy-timeout** - Maximal time (in seconds) for connection to keep alive. If no activity was detected in the session and the timeout has been reached, connection will be stopped. Default value: 60 seconds.

Journaling parameters
#####################

The following mount-point parameters relate with tracing of executed commands. It is not required to set these parameters.

* **cli-topology:journal-size** - Size of the cli mount-point journal. Journal keeps track of executed commands and makes  them available for users/apps for debugging purposes. Value 0 disables journaling (it is default value).
* **cli-topology:dry-run-journal-size** - Creates dry-run mount-point and defines number of commands in command history for dry-run mount-point. Value 0 disables dry-run functionality (it is default value).
* **cli-topology:journal-level** - Sets how much information should be stored in the journal. Option 'command-only' stores only the actual commands executed on device. Option 'extended' records additional information such as: transaction life-cycle, which handlers were invoked etc.

Other parameters
################

Other non-mandatory parameters that can be added to mount-request.

* **cli-topology:error-commit-patterns** - Device specific list of commit error patterns. The following list of patterns is checked in the input after 'commit' command is sent.
* **cli-topology:error-patterns** - Device specific list of error patterns. This list is the primary source of error checking on the device. The list defined by this parameter can override hardcoded one specified in the code.
* **cli-topology:parsing-engine** - Specification of the parsing system that is responsible for interpretation of device running-configuration. For now, supported methods are 'tree-parser' (default option) and 'batch-parser'.
* **node-extension:reconcile** - Whether to invoke reconciliation upon connection to a device.

.. note::

    More information about CLI layer can be found `here <../uniconfig_cli/cli-service-module.html>`__.

    :download:`cli-topology.yang <cli-topology.yang>`

    :download:`Tree representation of a topology <cli-topology.html>`

Example - mounting of Cisco XR device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Mounting of IOS-XR device (version 6.3.4) on address '192.168.1.211' with enabled dry-run functionality and explicitly set keepalive settings. Also, the privileged mode password is set using 'cli-topology:secret' since it is different from username password.

.. code:: bash

    curl -X PUT \
        http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=cli/node=iosxr \
        -H 'content-type: application/json' \
        -d '{
            "network-topology:node": {
                "network-topology:node-id": "iosxr",
                "cli-topology:host": "192.168.1.211",
                "cli-topology:port": "22",
                "cli-topology:transport-type": "ssh",
                "cli-topology:device-type": "ios xr",
                "cli-topology:device-version": "6.3.4",
                "cli-topology:username": "cisco",
                "cli-topology:password": "cisco",
                "cli-topology:journal-size": 150,
                "cli-topology:dry-run-journal-size": 200,
                "node-extension:reconcile": false
            }
        }'

Example - mounting of JUNOS device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Mounting of JUNOS device (version 17.3) on address '10.10.199.65' with disabled dry-run and kept default keepalive settings (they are not explicitly set in the request). JUNOS devices don't require privileged mode password 'cli-topology:secret' parameter doesn't have to be set at all.

.. code:: bash

    curl -X PUT \
        http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=cli/node=junos \
        -H 'content-type: application/json' \
        -d '{
            "network-topology:node": {
                "network-topology:node-id": "junos",
                "cli-topology:host": "10.10.199.65",
                "cli-topology:port": "22",
                "cli-topology:transport-type": "ssh",
                "cli-topology:device-type": "junos",
                "cli-topology:device-version": "17.3",
                "cli-topology:username": "root",
                "cli-topology:password": "root"
            }
        }'

.. note::

Example - mounting of generic Linux device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It is possible to mount any network device as a generic device. This allows invocation of any commands on the device using RPCs, which return the output back as freeform data and it is up to the user/application to make sense of them.

The following example shows how to mount generic CLI device on address '10.10.199.157'. Note that 'telnet' protocol is used for communication with device and both 'device-type' and 'device-version' are set to '*' (there are not translation units for unknown device type).

.. code:: bash

    curl -X PUT \
        http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=cli/node=linux \
        -H 'content-type: application/json' \
        -d '{
            "network-topology:node": {
                "network-topology:node-id": "linux",
                "cli-topology:host": "10.10.199.157",
                "cli-topology:port": "23",
                "cli-topology:transport-type": "telnet",
                "cli-topology:device-type": "*",
                "cli-topology:device-version": "*",
                "cli-topology:username": "test",
                "cli-topology:password": "frinx"
            }
        }'

Example - unmounting of CLI device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To unmount CLI device from all layers, it is necessary to call HTTP DELETE request to specific node. An example shows how to remove previously created 'junos' mount-point.

.. code:: bash

    curl -X DELETE \
        http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=cli/node=junos

Mounting of NETCONF device
--------------------------

Description of mount-request parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Identification of remote device
###############################

List of basic connection parameters that are used for identification of remote device. Only 'tcp-only' parameter must not be specified in input of the request.

* **network-topology:node-id** -  Name of node that represents device / mount-point in the topology.
* **netconf-node-topology:host** - IP address or domain-name of target device that runs NETCONF server.
* **netconf-node-topology:port** - TCP port on which NETCONF server is listening to incoming connections.
* **netconf-node-topology:tcp-only** - If it is set to 'true', NETCONF session is created directly on top of TCP connection. Otherwise, 'SSH' is used as carriage protocol. By default, this parameter is set to 'false'.

Authentication parameters
#########################

Parameters used for configuration of the basic authentication method against NETCONF server. These parameters must be specified in the input request.

* **network-topology:username** - Name of the user that has permission to access device using NETCONF management line.
* **network-topology:password** - Password to the user in non-encrypted format.

.. note::

    There are also other authentication parameters if different authentication method is used - for example, key-based authentication requires specification of key-id. All available authentication parameters can be found in 'netconf-node-topology.yang' under 'netconf-node-credentials' grouping.

Session timers
##############

The following parameters adjust timers that are related with maintaining of NETCONF session state. None of these parameters are mandatory (default values will be used).

* **netconf-node-topology:connection-timeout-millis** - Specifies timeout in milliseconds after which initial connection to the NETCONF server must be established (default value: 20000 ms).
* **netconf-node-topology:default-request-timeout-millis** - Timeout for blocking RPC operations within transactions (default value: 60000 ms).
* **netconf-node-topology:max-connection-attempts** - Maximum number of connection attempts (default value: 0 - disabled).
* **netconf-node-topology:between-attempts-timeout-millis** - Initial timeout between reconnection attempts (default value: 2000 ms).
* **netconf-node-topology:sleep-factor** - Multiplier between subsequent delays of reconnection attempts (default value: 1.5).
* **netconf-node-topology:keepalive-delay** - Delay between sending of keepalive RPC messages (default value: 120 sec).

Capabilities
############

Parameters related to capabilities are often used when NETCONF device doesn't provide list of YANGs. Both parameters are optional.

* **netconf-node-topology:yang-module-capabilities** - Set a list of capabilities to override capabilities provided in device's hello message. It can be used for devices that do not report any yang modules in their hello message.
* **netconf-node-topology:non-module-capabilities** - Set a list of non-module based capabilities to override or merge non-module capabilities provided in device's hello message. It can be used for devices that do not report or incorrectly report non-module-based capabilities in their hello message.

.. note::

    Instead of defining "netconf-node-topology:yang-module-capabilities", we can just define folder with yang schemas "netconf-node-topology:schema-cache-directory": "folder-name". For more information about using the "netconf-node-topology:schema-cache-directory" parameter, see :ref:`RST Other parameters`.

UniConfig-native
################

Parameters related to installation of NETCONF or CLI nodes with uniconfig-native support.

* **uniconfig-config:uniconfig-native-enabled** - Whether uniconfig-native should be used for installation of NETCONF or CLI node. By default, this flag is set to 'false'.
* **uniconfig-config:install-uniconfig-node-enabled** - Whether node should be installed to UniConfig and unified layers. By default, this flag is set to 'true'.
* **uniconfig-config:sequence-read-active** - Forces reading of data sequentially when mounting device. By default, this flag is set to 'false'. This parameter has effect only on NETCONF nodes.
* **uniconfig-config:blacklist** - List of root YANG entities that should not be read from NETCONF device due to incompatibility with uniconfig-native or other malfunctions in YANG schemas. This parameter has effect only on NETCONF nodes.
* **uniconfig-config:validation-enabled** - Whether validation RPC should be used before submitting configuration of node. By default, this flag is set to 'true'. This parameter has effect only on NETCONF nodes.
* **uniconfig-config:confirmed-commit-enabled** - Whether confirmed-commit RPC should be used before submitting configuration of node. By default, this flag is set to 'true'. This parameter has effect only on NETCONF nodes.

.. note::

    More information and examples about uniconfig-native functionality can be found `here <../uniconfig-native_netconf/index.html>`__.

.. _RST Other parameters:

Other parameters
################

Other non-mandatory parameters that can be added to mount-request.

* **netconf-node-topology:schema-cache-directory** - This parameter can be used for two cases:

    #. Explicitly set name of NETCONF cache directory. If it is not set, the name of the schema cache directory is derived from device capabilities during mounting process.
    #. Direct usage of the 'custom' NETCONF cache directory stored in the UniConfig 'cache' directory by name. This 'custom' directory must exist, must not be empty and also can not use the 'netconf-node-topology:yang-module-capabilities' parameter, because capability names will be generated from yang schemas stored in the 'custom' directory.

* **netconf-node-topology:dry-run-journal-size** - Creates dry-run mount-point and defines number of NETCONF RPCs in history for dry-run mount-point. Value 0 disables dry-run functionality (it is default value).
* **netconf-node-topology:customization-factory** - Specification of the custom NETCONF connector factory. For example, if device doesn't support candidate data-store, this parameter should be set to 'netconf-customization-alu-ignore-candidate' string.
* **netconf-node-topology:edit-config-test-option** - Specification of the test-option parameter in the netconf edit-config message. Possible values are 'set', 'test-then-set' or 'test-only'. If the edit-config-test-option is not explicitly specified in the mount request, then the default value will be used ('test-then-set'). See `RFC-6241 <https://tools.ietf.org/html/rfc6241#section-8.6>`_ for more information about this feature.
* **netconf-node-topology:confirm-timeout** - The timeout for confirming the configuration by "confirming-commit" that was configured by "confirmed-commit" (default value: 600 sec). Configuration will be automatically reverted by device if the "confirming-commit" is not issued within the timeout period. This parameter has effect only on NETCONF nodes.
* **netconf-node-topology:strict-parsing** - Default value of strict-parsing parameter is set to 'true'. This may inflicts in throwing exception during parsing of received NETCONF messages in case of unknown elements. If this parameter is set to 'false', then parser should ignore unknown elements and not throw exception during parsing.

.. note::

    The edit-config-test-option is only supported if the device advertises the :validate:1.0 or :validate:1.1 capability.

    The 'test-only' value of the 'edit-config-test-option' parameter is not currently supported.

.. note::

    More information about NETCONF layer can be found `here <../uniconfig_netconf/netconf-intro.html>`__.

    :download:`netconf-node-topology.yang <netconf-node-topology.yang>`

    :download:`Tree representation of netconf-node-topology.yang <netconf-node-topology.html>`

Example - mounting of JUNOS device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This example shows how to mount JUNOS device on address '10.10.199.65' that has NETCONF server listening on port 830. Additionally, keepalive delay is explicitly set and dry-run functionality is enabled for this mount-point.

.. code:: bash

    curl -X PUT \
        http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=junos \
        -H 'content-type: application/json' \
        -d '{
            "node": [
                {
                    "node-id": "junos",
                    "netconf-node-topology:host": "10.10.199.65",
                    "netconf-node-topology:port": 830,
                    "netconf-node-topology:keepalive-delay": 10,
                    "netconf-node-topology:tcp-only": false,
                    "netconf-node-topology:username": "root",
                    "netconf-node-topology:password": "root",
                    "netconf-node-topology:dry-run-journal-size": 100
                }
            ]
        }'

Example - mounting of Nokia SROS device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This example shows mounting of NETCONF device that doesn't report all YANG models and also doesn't support candidate data-store. From this reason it is required to explicitly list YANG capabilities ('yang-module-capabilities' container) and specify custom NETCONF connector factory that ignores candidate data-store. Note that this example also shows explicitly set keepalive, delay and timeout parameters.

.. code:: bash

    curl -X PUT \
        http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=sros \
        -H 'content-type: application/json' \
        -d '{
            "network-topology:node": {
                "node-id": "sros",
                "netconf-node-topology:host": "10.19.0.18",
                "netconf-node-topology:port": 1830,
                "netconf-node-topology:tcp-only": false,
                "netconf-node-topology:username": "admin",
                "netconf-node-topology:password": "admin",
                "netconf-node-topology:keepalive-delay": 10,
                "netconf-node-topology:connection-timeout-millis": 60000,
                "netconf-node-topology:default-request-timeout-millis": 60000,
                "netconf-node-topology:sleep-factor": 1,
                "netconf-node-topology:customization-factory": "netconf-customization-alu-ignore-candidate",
                "netconf-node-topology:yang-module-capabilities": {
                    "capability": [
                        "urn:ietf:params:xml:ns:yang:ietf-inet-types?module=ietf-inet-types&amp;revision=2010-09-24",
                        "urn:ietf:params:xml:ns:netconf:base:1.0?module=ietf-netconf&amp;revision=2011-06-01",
                        "urn:nokia.com:sros:ns:yang:sr:conf?module=nokia-conf-log&amp;revision=2016-07-11",
                        "urn:nokia.com:sros:ns:yang:sr:conf?module=nokia-conf-python&amp;revision=2016-07-11",
                        "urn:nokia.com:sros:ns:yang:sr:conf?module=nokia-conf-qos&amp;revision=2016-07-15",
                        "urn:nokia.com:sros:ns:yang:sr:conf?module=nokia-conf-service&amp;revision=2016-07-13",
                        "urn:nokia.com:sros:ns:yang:sr:conf?module=nokia-conf-system&amp;revision=2016-07-13"
                    ]
                }
            }
        }'

Example - mounting of IOS XR device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This example shows mounting of NETCONF device that doesn't report all YANG models. From this reason it is required to explicitly list YANG capabilities ('yang-module-capabilities' container) or define folder with yang models ('netconf-node-topology:schema-cache-directory': 'folder_name'). This example describes the second option.

.. code:: bash

    curl -X PUT \
        http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=xr6 \
        -H 'content-type: application/json' \
        -d '{
            "network-topology:node": {
                "node-id": "xr6",
                "netconf-node-topology:host": "192.168.1.216",
                "netconf-node-topology:port": 830,
                "netconf-node-topology:keepalive-delay": 0,
                "netconf-node-topology:tcp-only": false,
                "netconf-node-topology:username": "cisco",
                "netconf-node-topology:password": "cisco",
                "uniconfig-config:uniconfig-native-enabled": true,
                "netconf-node-topology:edit-config-test-option": "set",
                "netconf-node-topology:schema-cache-directory": "folder_name"
            }
        }'

Example - mounting of uniconfig-native NETCONF device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For mounting of NETCONF device with uniconfig-native support, it is necessary to explicitly enable this functionality using 'uniconfig-native-enabled' flag and optionally specify blacklist-ed paths and/or extensions that cannot be synced from device because of non-compatibility or malfunctions in YANG schemas. Another optional flag is 'sequence-read-active', which is used for forced reading of data sequentially when mounting device. The default value for this flag is false.

.. code:: bash

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
                "uniconfig-config:sequence-read-active": true,
                "uniconfig-config:blacklist": {
                    "uniconfig-config:path": ["openconfig-interfaces:interfaces", "ietf-interfaces:interfaces", "openconfig-vlan:vlans", "openconfig-routing-policy:routing-policy"],
                    "uniconfig-config:extension": ["tailf:display-when false"]
                }
            }
        ]
    }'

Example - mounting of uniconfig-native CLI device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For mounting of CLI device with uniconfig-native support, it is necessary to explicitly enable this functionality using 'uniconfig-native-enabled' flag. The following example shows request used for mounting of JUNOS 17.3 device as native-CLI device with enabled dry-run functionality and disabled reconciliation.

.. code:: bash

    curl --request PUT 'http://localhost:8181/rests/data/network-topology:network-topology/topology=cli/node=junos' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "network-topology:node": {
            "network-topology:node-id": "junos",
            "cli-topology:host": "192.168.1.247",
            "cli-topology:port": "22",
            "cli-topology:transport-type": "ssh",
            "cli-topology:device-type": "junos",
            "cli-topology:device-version": "17.3",
            "cli-topology:username": "root",
            "cli-topology:password": "junos17pass",
            "cli-topology:journal-size": 150,
            "cli-topology:dry-run-journal-size": 180,
            "node-extension:reconcile": false,
            "uniconfig-config:uniconfig-native-enabled": true
        }
    }'

Example - unmounting of NETCONF device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To unmount NETCONF device from all layers, it is necessary to call HTTP DELETE request to specific node. An example shows how to remove previously created 'sros' mount-point.

.. code:: bash

    curl -X DELETE \
        http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=sros
